# CSV Export Feature - Implementation Plan

## Goal
Add a CSV export endpoint to an existing Express + TypeScript service so users can export query results from PostgreSQL into a correctly escaped CSV consumable by Excel and other tools.

## Constraints
- Backend: Express + TypeScript, PostgreSQL
- Frontend: React (will call endpoint and trigger download)
- Must handle Unicode (Chinese) and fields containing commas, newlines, quotes
- Performance: support exports up to 50k rows without OOM on server (streaming preferred)
- Security: only authenticated users with correct permissions may export

## Milestones

1. Schema & Query Design
   - Task 1.1: Identify target queries/endpoints and required columns
     - inputs: DB schema, example query requirements
     - outputs: documented SQL query templates
     - acceptance_criteria:
       - SQL template returns correct columns on sample DB
     - estimate: 2h
     - priority: high

2. Backend API Implementation
   - Task 2.1: Add GET /api/export/csv?report=... endpoint
     - inputs: query params, auth context
     - outputs: HTTP response stream with Content-Type: text/csv and proper Content-Disposition
     - acceptance_criteria:
       - Endpoint streams rows as CSV (no full materialization into memory)
       - Response includes header row with correct column names
       - For sample small dataset, CSV content exactly matches expected (see evals)
     - estimate: 1d
     - priority: high

   - Task 2.2: Implement server-side CSV escaping & streaming
     - inputs: pg cursor / node-postgres query stream
     - outputs: CSV stream with RFC4180-compatible escaping (double quotes, commas, newlines handled)
     - acceptance_criteria:
       - Fields with commas/quotes/newlines are correctly quoted and escaped
       - Memory usage stays bounded for large exports (verify with 100k rows test)
     - estimate: 1d
     - priority: high

3. Frontend Integration
   - Task 3.1: Add download button and fetch logic in React
     - inputs: frontend repo, auth token flow
     - outputs: client that triggers download and saves file
     - acceptance_criteria:
       - Clicking download triggers file download with correct filename and content
     - estimate: 4h
     - priority: medium

4. Permissions & Rate Limiting
   - Task 4.1: Enforce permission checks and add rate limits to prevent abuse
     - inputs: auth system, rate limit policy
     - outputs: middleware that verifies permissions and enforces rate limits
     - acceptance_criteria:
       - Unauthorized user receives 403
       - Rate-limited requests receive 429
     - estimate: 4h
     - priority: medium

5. Tests & Verification
   - Task 5.1: Add unit and integration tests (including streaming, escaping)
     - inputs: test DB fixtures, test framework
     - outputs: automated tests that assert CSV outputs
     - acceptance_criteria:
       - All tests pass in CI
     - estimate: 1d
     - priority: high

## Deliverables
- plan.md (this file)
- todo list (short) for tasks
- evals/evals.json updated with two concrete test scenarios (see workspace/evals-outputs.json)

## Risks
- Large exports may impact DB or application performance — mitigate with cursors/pagination and queueing if needed.
- CSV consumers may expect specific encodings (UTF-8 vs BOM for Excel). Decide whether to include UTF-8 BOM for Windows Excel compatibility.
