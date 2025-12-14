```bash 
#!/bin/bash

# Terraform Background Runner
# This script runs terraform wrapper scripts in background with logging and process management

WORK_DIR="${WORK_DIR:-$(pwd)}"
LOG_DIR="${LOG_DIR:-$HOME/tf-logs}"
PID_FILE="$LOG_DIR/terraform.pid"
LOG_FILE="$LOG_DIR/terraform-$(date +%Y%m%d-%H%M%S).log"

# Terraform scripts location
SCRIPT_DIR="${SCRIPT_DIR:-./platform/scripts}"
PLAN_SCRIPT="$SCRIPT_DIR/terraform-plan.sh"
APPLY_SCRIPT="$SCRIPT_DIR/terraform-apply.sh"

# Create log directory
mkdir -p "$LOG_DIR"

# Function to start terraform script in background
start_terraform() {
    local script_path="$1"
    local script_name="$2"
    
    if [ ! -f "$script_path" ]; then
        echo "Error: Script not found: $script_path"
        echo "Please check the SCRIPT_DIR variable or run from the correct directory"
        return 1
    fi
    
    if [ -f "$PID_FILE" ]; then
        local pid=$(cat "$PID_FILE")
        if ps -p "$pid" > /dev/null 2>&1; then
            echo "Terraform is already running (PID: $pid)"
            echo "Use './tf-runner.sh status' to check or './tf-runner.sh stop' to stop it"
            return 1
        fi
    fi
    
    echo "Starting: $script_path"
    echo "Log file: $LOG_FILE"
    
    cd "$WORK_DIR"
    
    # Run terraform script in background with nohup, redirect all output to log
    nohup "$script_path" > "$LOG_FILE" 2>&1 &
    
    local pid=$!
    echo $pid > "$PID_FILE"
    
    # Make process immune to HUP signal
    disown $pid 2>/dev/null || true
    
    echo "Terraform $script_name started in background (PID: $pid)"
    echo "To view logs: tail -f $LOG_FILE"
    echo "To attach: ./tf-runner.sh attach"
}

# Function to check status
status_terraform() {
    if [ ! -f "$PID_FILE" ]; then
        echo "No terraform process found"
        return 1
    fi
    
    local pid=$(cat "$PID_FILE")
    
    if ps -p "$pid" > /dev/null 2>&1; then
        echo "Terraform is running (PID: $pid)"
        
        # Find the most recent log file
        local latest_log=$(ls -t "$LOG_DIR"/terraform-*.log 2>/dev/null | head -1)
        if [ -n "$latest_log" ]; then
            echo "Log file: $latest_log"
            echo ""
            echo "Last 10 lines:"
            tail -10 "$latest_log"
        fi
    else
        echo "Terraform process (PID: $pid) is not running"
        rm -f "$PID_FILE"
        return 1
    fi
}

# Function to attach to logs (view in real-time)
attach_terraform() {
    if [ ! -f "$PID_FILE" ]; then
        echo "No terraform process found"
        return 1
    fi
    
    local pid=$(cat "$PID_FILE")
    
    if ! ps -p "$pid" > /dev/null 2>&1; then
        echo "Terraform process (PID: $pid) is not running"
        rm -f "$PID_FILE"
        return 1
    fi
    
    local latest_log=$(ls -t "$LOG_DIR"/terraform-*.log 2>/dev/null | head -1)
    
    if [ -z "$latest_log" ]; then
        echo "No log file found"
        return 1
    fi
    
    echo "Attaching to terraform logs (PID: $pid)"
    echo "Press Ctrl+C to detach (terraform will keep running)"
    echo ""
    
    tail -f "$latest_log"
}

# Function to stop terraform
stop_terraform() {
    if [ ! -f "$PID_FILE" ]; then
        echo "No terraform process found"
        return 1
    fi
    
    local pid=$(cat "$PID_FILE")
    
    if ps -p "$pid" > /dev/null 2>&1; then
        echo "Stopping terraform (PID: $pid)"
        kill $pid
        sleep 2
        
        if ps -p "$pid" > /dev/null 2>&1; then
            echo "Force stopping..."
            kill -9 $pid
        fi
        
        rm -f "$PID_FILE"
        echo "Terraform stopped"
    else
        echo "Terraform process (PID: $pid) is not running"
        rm -f "$PID_FILE"
    fi
}

# Function to list all logs
list_logs() {
    echo "Available log files:"
    ls -lht "$LOG_DIR"/terraform-*.log 2>/dev/null || echo "No logs found"
}

# Main script
case "${1:-}" in
    plan)
        start_terraform "$PLAN_SCRIPT" "plan"
        ;;
    apply)
        start_terraform "$APPLY_SCRIPT" "apply"
        ;;
    status)
        status_terraform
        ;;
    attach|logs)
        attach_terraform
        ;;
    stop|kill)
        stop_terraform
        ;;
    list)
        list_logs
        ;;
    *)
        echo "Terraform Background Runner"
        echo ""
        echo "Usage: $0 <command>"
        echo ""
        echo "Commands:"
        echo "  plan          - Run terraform-plan.sh in background"
        echo "  apply         - Run terraform-apply.sh in background"
        echo "  status        - Check if terraform is running"
        echo "  attach|logs   - View live logs (Ctrl+C to detach)"
        echo "  stop|kill     - Stop the running terraform process"
        echo "  list          - List all log files"
        echo ""
        echo "Environment variables:"
        echo "  WORK_DIR    - Working directory (default: current dir)"
        echo "  LOG_DIR     - Log directory (default: ~/tf-logs)"
        echo "  SCRIPT_DIR  - Terraform scripts location (default: ./platform/scripts)"
        echo ""
        echo "Examples:"
        echo "  $0 plan"
        echo "  $0 apply"
        echo "  $0 status"
        echo "  $0 attach"
        echo ""
        echo "Current script paths:"
        echo "  Plan:  $PLAN_SCRIPT"
        echo "  Apply: $APPLY_SCRIPT"
        exit 1
        ;;
esac
```