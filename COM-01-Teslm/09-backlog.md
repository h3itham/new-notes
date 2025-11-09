- Add this line to all Dockerfile 
	```txt
	# Copy built app and required assets
	COPY --from=builder /app/dist ./dist
	COPY --from=builder /app/public ./public
	COPY --from=builder /app/fonts ./fonts
	COPY --from=builder /app/assets ./assets   # ✅ Add this line
	```
- Nginx Configuration changed ()
- [ ] Fine tune MAP APIs  
- [ ] Search How can make ECS take role to talk with different AWS services specially with s3 bucket instead of using Access and secret access key.  
- [ ] 