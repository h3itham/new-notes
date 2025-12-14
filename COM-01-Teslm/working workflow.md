```yaml 
name: Deploy to AWS ECS

on:
  push:
    branches:
      - feat/cloudrun
  workflow_dispatch:

env:
  AWS_REGION: ${{ vars.AWS_REGION }}
  ECR_REPOSITORY: ${{ vars.ENVIRONMENT }}-container-repo
  ECS_CLUSTER: ${{ vars.ECS_CLUSTER_NAME }}
  ECS_SERVICE: ${{ vars.ECS_SERVICE_NAME }}
  ECS_TASK_DEFINITION: ${{ vars.ECS_TASK_DEFINITION_NAME }}
  CONTAINER_NAME: web-app

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: Prod
    permissions:
      contents: read
      id-token: write   # Required for OIDC authentication

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      # 🔐 Configure AWS credentials via OIDC (No static keys!)
      - name: Configure AWS credentials (OIDC)
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::242945234432:role/GitHubActionsDeployRole
          aws-region: ${{ env.AWS_REGION }}

      # 🐳 Login to Amazon ECR
      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      # 🧱 Build Docker image
      - name: Build Docker image
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          IMAGE_TAG: ${{ github.sha }}
        run: |
          docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .

      # 📦 Push Docker image to Amazon ECR
      - name: Push Docker image to Amazon ECR
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          IMAGE_TAG: ${{ github.sha }}
        run: |
          docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG

      # 🧾 Download current ECS task definition
      - name: Download current task definition
        run: |
          aws ecs describe-task-definition \
            --task-definition ${{ env.ECS_TASK_DEFINITION }} \
            --query taskDefinition > task-definition.json

      # 🧩 Update task definition with new image
      - name: Update task definition with new image
        id: task-def
        uses: aws-actions/amazon-ecs-render-task-definition@v1
        with:
          task-definition: task-definition.json
          container-name: ${{ env.CONTAINER_NAME }}
          image: ${{ steps.login-ecr.outputs.registry }}/${{ env.ECR_REPOSITORY }}:${{ github.sha }}
          environment-variables: |
            POSTGRES_HOST=${{ secrets.POSTGRES_HOST }}
            POSTGRES_DATABASE=${{ secrets.POSTGRES_DATABASE }}
            POSTGRES_USER=${{ secrets.POSTGRES_USER }}
            POSTGRES_PASSWORD=${{ secrets.POSTGRES_PASSWORD }}
            POSTGRES_PORT=${{ secrets.POSTGRES_PORT }}
            REDIS_HOST=${{ vars.REDIS_HOST }}
            REDIS_PORT=${{ vars.REDIS_PORT }}
            DB_SSL=${{ vars.DB_SSL }}
            MONGO_URI=${{ secrets.MONGO_URI }}
            MAIN_PORT=${{ vars.MAIN_PORT }}
            CHAT_PORT=${{ vars.CHAT_PORT }}
            DELIVERY_PARTNER_PORT=${{ vars.DELIVERY_PARTNER_PORT }}
            NOTIFICATION_PORT=${{ vars.NOTIFICATION_PORT }}
            MODE=${{ vars.MODE }}
            RUN_MIGRATIONS=false
            OTP_SERVER=${{ vars.OTP_SERVER }}
            MSEGAT_API_KEY=${{ secrets.MSEGAT_API_KEY }}
            MSEGAT_USERNAME=${{ secrets.MSEGAT_USERNAME }}
            MSEGAT_USER_SENDER=${{ secrets.MSEGAT_USER_SENDER }}
            GOOGLE_MAPS_API_KEY=${{ secrets.GOOGLE_MAPS_API_KEY }}
            ENCRYPTION_KEY=${{ secrets.ENCRYPTION_KEY }}
            AWS_REGION=${{ vars.AWS_REGION }}
            AWS_BUCKET=${{ vars.AWS_BUCKET }}
            WAREHOUSES_API_BASE_URL=${{ secrets.WAREHOUSES_API_BASE_URL }}
            WAREHOUSES_PASSWORD=${{ secrets.WAREHOUSES_PASSWORD }} 
            WAREHOUSES_USER=${{ secrets.WAREHOUSES_USER }}

      # 🚀 Deploy new task definition to ECS
      - name: Deploy to Amazon ECS
        uses: aws-actions/amazon-ecs-deploy-task-definition@v1
        with:
          task-definition: ${{ steps.task-def.outputs.task-definition }}
          service: ${{ env.ECS_SERVICE }}
          cluster: ${{ env.ECS_CLUSTER }}
          wait-for-service-stability: true

      # 🌐 Output ALB URL
      - name: Get ALB URL
        run: |
          echo "Service deployed successfully!"

          TARGET_GROUP_ARN=$(aws ecs describe-services \
            --cluster ${{ env.ECS_CLUSTER }} \
            --services ${{ env.ECS_SERVICE }} \
            --query 'services[0].loadBalancers[0].targetGroupArn' \
            --output text)

          if [ "$TARGET_GROUP_ARN" != "None" ] && [ -n "$TARGET_GROUP_ARN" ]; then
            LB_ARN=$(aws elbv2 describe-target-groups \
              --target-group-arns $TARGET_GROUP_ARN \
              --query 'TargetGroups[0].LoadBalancerArns[0]' \
              --output text)

            if [ "$LB_ARN" != "None" ] && [ -n "$LB_ARN" ]; then
              SERVICE_URL=$(aws elbv2 describe-load-balancers \
                --load-balancer-arns $LB_ARN \
                --query 'LoadBalancers[0].DNSName' \
                --output text)

              echo "::notice::Service deployed at http://$SERVICE_URL"
              echo "service_url=http://$SERVICE_URL" >> $GITHUB_OUTPUT
            else
              echo "No load balancer found for target group"
            fi
          else
            echo "No target group found for this service"
          fi

      # ✅ Verify deployment
      - name: Verify deployment
        run: |
          echo "Deployment completed at $(date)"
          aws ecs describe-services \
            --cluster ${{ env.ECS_CLUSTER }} \
            --services ${{ env.ECS_SERVICE }} \
            --query 'services[0].deployments' \
            --output table
```