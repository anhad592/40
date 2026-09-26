#====================================================================================================
# START - Testing Protocol - DO NOT EDIT OR REMOVE THIS SECTION
#====================================================================================================

# THIS SECTION CONTAINS CRITICAL TESTING INSTRUCTIONS FOR BOTH AGENTS
# BOTH MAIN_AGENT AND TESTING_AGENT MUST PRESERVE THIS ENTIRE BLOCK

# Communication Protocol:
# If the `testing_agent` is available, main agent should delegate all testing tasks to it.
#
# You have access to a file called `test_result.md`. This file contains the complete testing state
# and history, and is the primary means of communication between main and the testing agent.
#
# Main and testing agents must follow this exact format to maintain testing data. 
# The testing data must be entered in yaml format Below is the data structure:
# 
## user_problem_statement: {problem_statement}
## backend:
##   - task: "Task name"
##     implemented: true
##     working: true  # or false or "NA"
##     file: "file_path.py"
##     stuck_count: 0
##     priority: "high"  # or "medium" or "low"
##     needs_retesting: false
##     status_history:
##         -working: true  # or false or "NA"
##         -agent: "main"  # or "testing" or "user"
##         -comment: "Detailed comment about status"
##
## frontend:
##   - task: "Task name"
##     implemented: true
##     working: true  # or false or "NA"
##     file: "file_path.js"
##     stuck_count: 0
##     priority: "high"  # or "medium" or "low"
##     needs_retesting: false
##     status_history:
##         -working: true  # or false or "NA"
##         -agent: "main"  # or "testing" or "user"
##         -comment: "Detailed comment about status"
##
## metadata:
##   created_by: "main_agent"
##   version: "1.0"
##   test_sequence: 0
##   run_ui: false
##
## test_plan:
##   current_focus:
##     - "Task name 1"
##     - "Task name 2"
##   stuck_tasks:
##     - "Task name with persistent issues"
##   test_all: false
##   test_priority: "high_first"  # or "sequential" or "stuck_first"
##
## agent_communication:
##     -agent: "main"  # or "testing" or "user"
##     -message: "Communication message between agents"

# Protocol Guidelines for Main agent
#
# 1. Update Test Result File Before Testing:
#    - Main agent must always update the `test_result.md` file before calling the testing agent
#    - Add implementation details to the status_history
#    - Set `needs_retesting` to true for tasks that need testing
#    - Update the `test_plan` section to guide testing priorities
#    - Add a message to `agent_communication` explaining what you've done
#
# 2. Incorporate User Feedback:
#    - When a user provides feedback that something is or isn't working, add this information to the relevant task's status_history
#    - Update the working status based on user feedback
#    - If a user reports an issue with a task that was marked as working, increment the stuck_count
#    - Whenever user reports issue in the app, if we have testing agent and task_result.md file so find the appropriate task for that and append in status_history of that task to contain the user concern and problem as well 
#
# 3. Track Stuck Tasks:
#    - Monitor which tasks have high stuck_count values or where you are fixing same issue again and again, analyze that when you read task_result.md
#    - For persistent issues, use websearch tool to find solutions
#    - Pay special attention to tasks in the stuck_tasks list
#    - When you fix an issue with a stuck task, don't reset the stuck_count until the testing agent confirms it's working
#
# 4. Provide Context to Testing Agent:
#    - When calling the testing agent, provide clear instructions about:
#      - Which tasks need testing (reference the test_plan)
#      - Any authentication details or configuration needed
#      - Specific test scenarios to focus on
#      - Any known issues or edge cases to verify
#
# 5. Call the testing agent with specific instructions referring to test_result.md
#
# IMPORTANT: Main agent must ALWAYS update test_result.md BEFORE calling the testing agent, as it relies on this file to understand what to test next.

#====================================================================================================
# END - Testing Protocol - DO NOT EDIT OR REMOVE THIS SECTION
#====================================================================================================



#====================================================================================================
# Testing Data - Main Agent and testing sub agent both should log testing data below this section
#====================================================================================================

user_problem_statement: "Verify OTP LOGIN two-step verification bug fix - per-user otp_login toggle now honored (OTP_LOGIN_ENABLED changed from False to True)"

backend:
  - task: "OTP LOGIN Bug Fix Verification"
    implemented: true
    working: true
    file: "/app/backend/server.py"
    stuck_count: 0
    priority: "high"
    needs_retesting: false
    status_history:
      - working: true
        agent: "testing"
        comment: "BUG FIX VERIFIED: The per-user otp_login toggle is now correctly honored. Previously OTP_LOGIN_ENABLED was hardcoded to False (line 779), now set to True. Comprehensive test suite executed with 6 test cases, all passed: (1) Admin with otp_login=False gets token directly without OTP challenge ✓ (2) Admin can enable OTP for user via PATCH /users/{uid}/otp ✓ (3) User with otp_login=True receives OTP challenge (otp_required=true, challenge_id) instead of token ✓ (4) OTP code retrieved from backend logs (/var/log/supervisor/backend.err.log) and verified successfully, returns valid JWT token ✓ (5) Incorrect OTP code correctly rejected with 401 status ✓ (6) Disabling OTP for user restores direct token login ✓. The bug is FIXED - users with otp_login ON are now required to complete OTP verification, users with otp_login OFF get direct token access."

frontend:
  # No frontend testing performed as per system prompt instructions

metadata:
  created_by: "testing_agent"
  version: "1.1"
  test_sequence: 2
  run_ui: false
  last_tested: "2026-09-26T19:30:00Z"

test_plan:
  current_focus: []
  stuck_tasks: []
  test_all: false
  test_priority: "high_first"

agent_communication:
  - agent: "testing"
    message: "OTP LOGIN BUG FIX VERIFICATION COMPLETE - All tests passed (6/6). The reported bug is FIXED. Previously, OTP_LOGIN_ENABLED was hardcoded to False at line 779 in server.py, causing the per-user otp_login toggle to be ignored. This has been changed to True. Verified end-to-end: users with otp_login=True now correctly receive OTP challenges and must verify before getting a token; users with otp_login=False get direct token access without OTP. OTP codes are logged to /var/log/supervisor/backend.err.log for testing/recovery. Negative test (wrong OTP) correctly returns 401. Toggle functionality (PATCH /users/{uid}/otp) works bidirectionally. Test credentials: admin/admin123 (otp_login=False), user/user123 (toggled during test). Backend test script updated at /app/backend_test.py."
