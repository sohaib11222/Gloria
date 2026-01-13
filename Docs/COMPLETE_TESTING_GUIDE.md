# Complete Testing Guide for Car Hire Middleware

## Overview

This comprehensive testing guide is based on client requirements and conversation materials. It provides step-by-step instructions to test every aspect of your Car Hire Middleware system before sending it to the client for review.

**Table of Contents:**
1. [Pre-Testing Setup](#pre-testing-setup)
2. [Registration & Authentication Testing](#registration--authentication-testing)
3. [Echo/Ping API Testing](#echoping-api-testing)
4. [System Verification Testing](#system-verification-testing)
5. [Agreement Flow Testing](#agreement-flow-testing)
6. [Location Management Testing](#location-management-testing)
7. [Availability Endpoint Testing](#availability-endpoint-testing)
8. [Booking Operations Testing](#booking-operations-testing)
9. [Admin Panel Testing](#admin-panel-testing)
10. [Source Health & Performance Testing](#source-health--performance-testing)
11. [SDK Testing](#sdk-testing)
12. [Load Testing & Scaling](#load-testing--scaling)
13. [End-to-End Integration Testing](#end-to-end-integration-testing)
14. [Pre-Delivery Checklist](#pre-delivery-checklist)

---

## Pre-Testing Setup

### 1. Server Setup

#### Start the Backend Middleware
```bash
cd gloriaconnect_backend
npm install
npm run prisma:gen
npm run prisma:migrate
npm run prisma:seed
npm run seed:unlocode
npm run dev
```

**Verify:**
- Server listens on port 8080 (or configured PORT)
- Database connection successful
- Health endpoint responds: `http://localhost:8080/health`

#### Start Example Agent Backend
```bash
cd gloriaconnect_agent_backend  # or your agent example
npm install
npm run dev
```

#### Start Example Source Backend
```bash
cd gloriaconnect_source_backend  # or your source example
npm install
npm run dev
```

#### Start Frontend Applications
```bash
# Admin Panel
cd gloriaconnect_admin
npm install
npm run dev

# Agent Frontend (optional)
cd gloriaconnect_agent
npm install
npm run dev
```

### 2. Database Setup

**Verify Database:**
```bash
npm run test:db
```

**Seed Test Data:**
```bash
npm run tsx scripts/seed-test-data.ts
```

**Expected Test Data:**
- Admin user: `admin@test.com` / `admin123`
- Test Agents: `agent1@test.com`, `agent2@test.com` / `agent123`
- Test Sources: `source1@test.com`, `source2@test.com` / `source123`
- Test Agreements: `TEST-AGR-001`, `TEST-AGR-002`
- Test Locations: `GBMAN`, `GBGLA`, `USNYC`, `USLAX`, `FRPAR`

### 3. Environment Variables

**Required `.env` variables:**
```env
DATABASE_URL="mysql://user:password@localhost:3306/car_hire_mw"
JWT_SECRET="your-secret-key"
PORT=8080
NODE_ENV=development
```

---

## Registration & Authentication Testing

### Test 1: Agent Registration

**Endpoint:** `POST /api/auth/register`

**Request:**
```json
{
  "email": "test-agent@example.com",
  "companyName": "Test Agent Company",
  "password": "TestPass123!",
  "type": "AGENT"
}
```

**Expected Response:**
- Status: `201 Created`
- Response includes `id`, `email`, `companyName`, `type`
- Email verification pending

**Verify:**
- ✅ Company created in database
- ✅ Password is hashed (not plain text)
- ✅ Type is set to `AGENT`
- ✅ Email verification status is `PENDING`

### Test 2: Source Registration

**Request:**
```json
{
  "email": "test-source@example.com",
  "companyName": "Test Source Company",
  "password": "TestPass123!",
  "type": "SOURCE"
}
```

**Expected Response:**
- Status: `201 Created`
- Response includes source-specific fields if any

**Verify:**
- ✅ Company created with type `SOURCE`
- ✅ Can configure gRPC endpoint later

### Test 3: Login

**Endpoint:** `POST /api/auth/login`

**Request:**
```json
{
  "email": "test-agent@example.com",
  "password": "TestPass123!"
}
```

**Expected Response:**
- Status: `200 OK`
- Returns JWT token
- Returns user/company information

**Verify:**
- ✅ Token is valid JWT format
- ✅ Token includes user ID and company ID
- ✅ Token expires after configured time
- ✅ Can use token for authenticated requests

### Test 4: Token Validation

**Endpoint:** `GET /api/auth/me` or any protected endpoint

**Headers:**
```
Authorization: Bearer <token>
```

**Verify:**
- ✅ Valid token allows access
- ✅ Invalid token returns `401 Unauthorized`
- ✅ Expired token returns `401 Unauthorized`
- ✅ Missing token returns `401 Unauthorized`

---

## Echo/Ping API Testing

### Background
The Echo/Ping API is the foundation for all other operations. It tests the submit → poll pattern and validates the core infrastructure.

### Test 1: Submit Echo Request

**Endpoint:** `POST /admin/grpc/echo/submit` (Admin endpoint) or gRPC `SubmitEcho`

**Request:**
```json
{
  "agreement_ref": "TEST-AGR-001",
  "message": "test-echo-123"
}
```

**Expected Response:**
```json
{
  "request_id": "req_abc123xyz",
  "status": "PENDING"
}
```

**Verify:**
- ✅ Unique request ID is generated
- ✅ Request ID format is consistent
- ✅ Agreement reference is validated
- ✅ Request is stored in database/queue

### Test 2: Poll Echo Results

**Endpoint:** `GET /admin/grpc/echo/poll?request_id=req_abc123xyz&since_seq=0&wait_ms=10000`

**Expected Response (Initial):**
```json
{
  "items": [
    {
      "source_id": "source1",
      "message": "test-echo-123",
      "sequence": 1
    }
  ],
  "status": "PARTIAL",
  "cursor": 1
}
```

**Expected Response (Complete):**
```json
{
  "items": [...],
  "status": "COMPLETE",
  "cursor": 3
}
```

**Verify:**
- ✅ Long polling works (waits up to `wait_ms`)
- ✅ Partial results are returned as sources respond
- ✅ Sequence numbers increment correctly
- ✅ Cursor tracks progress
- ✅ Status changes from `PARTIAL` to `COMPLETE`
- ✅ Results are finalized when all sources respond

### Test 3: Multiple Sources Echo

**Setup:** Configure 3+ test sources with the same agreement

**Submit Echo:**
- Request fans out to all active sources
- Each source responds independently

**Verify:**
- ✅ All sources receive the request
- ✅ Results are aggregated correctly
- ✅ Sequence numbers are unique per source
- ✅ Complete status only when ALL sources finish

### Test 4: Echo Timeout

**Submit Echo with slow sources:**
- Configure one source to respond slowly (>3s)
- Verify timeout behavior

**Verify:**
- ✅ Slow sources are excluded after 3 strikes (15min → 30min → 60min backoff)
- ✅ Results are finalized as `COMPLETE` even if some sources timeout
- ✅ Health metrics track slow sources

### Test 5: Echo Error Handling

**Test Scenarios:**
1. Invalid agreement reference
2. Non-existent request ID
3. Network errors to sources
4. Source returns error

**Verify:**
- ✅ Appropriate error messages
- ✅ Errors are logged
- ✅ System continues functioning

---

## System Verification Testing

### Background
Both Agents and Sources must pass verification before accessing live agreements.

### Test 1: Source Verification (Automated API Test)

**Requirements from Client:**
- Sources enter a test Agreement ID
- System runs automated API test suite
- Tests all required endpoints (Health, Locations, Availability, Bookings)

**Test Steps:**

1. **Create Test Agreement:**
   ```
   Agreement ID: TEST-VERIFY-SOURCE-001
   Status: PENDING_VERIFICATION
   ```

2. **Source Configures Endpoint:**
   - gRPC endpoint: `localhost:50052`
   - HTTP endpoint (if applicable)

3. **Run Verification:**
   **Endpoint:** `POST /api/verification/source/test`

   **Request:**
   ```json
   {
     "agreement_ref": "TEST-VERIFY-SOURCE-001",
     "test_grpc": true,
     "test_http": true
   }
   ```

   **Verification Tests Should:**
   - ✅ Test Health endpoint
   - ✅ Test Locations endpoint
   - ✅ Test Availability endpoint (SubmitEcho or SubmitAvailability)
   - ✅ Test Booking endpoints (Create, Modify, Cancel, Check)
   - ✅ Verify response formats
   - ✅ Check response times (< 3s threshold)

4. **Expected Response:**
   ```json
   {
     "status": "PASSED",
     "tests": {
       "health": { "ok": true, "ms": 45 },
       "locations": { "ok": true, "ms": 120 },
       "availability": { "ok": true, "ms": 250 },
       "bookings": { "ok": true, "ms": 180 }
     },
     "overall_time": 595
   }
   ```

5. **Verify:**
   - ✅ All required endpoints respond correctly
   - ✅ Response times are acceptable
   - ✅ Agreement status changes to `ACTIVE` if passed
   - ✅ Agreement status remains `PENDING_VERIFICATION` if failed
   - ✅ Detailed error messages if any test fails

### Test 2: Agent Verification (Manual Booking Test)

**Requirements from Client:**
- Agents create, modify, and cancel bookings against test data
- Uses test agreements, not live data
- Ensures integration works before access to real agreements

**Test Steps:**

1. **Create Test Agreement for Agent:**
   ```
   Agreement ID: TEST-VERIFY-AGENT-001
   Status: PENDING_VERIFICATION
   Test Mode: true
   ```

2. **Agent Tests Booking Creation:**
   **Endpoint:** `POST /bookings`
   
   **Request:**
   ```json
   {
     "agreement_ref": "TEST-VERIFY-AGENT-001",
     "supplier_id": "TEST-SUPPLIER",
     "offer_id": "TEST-OFFER-123",
     "driver": {
       "first_name": "Test",
       "last_name": "User",
       "email": "test@example.com",
       "age": 30
     }
   }
   ```

3. **Agent Tests Booking Modification:**
   **Endpoint:** `PATCH /bookings/{supplier_booking_ref}?agreement_ref=TEST-VERIFY-AGENT-001`
   
   **Request:**
   ```json
   {
     "driver": {
       "email": "updated@example.com"
     }
   }
   ```

4. **Agent Tests Booking Cancellation:**
   **Endpoint:** `POST /bookings/{supplier_booking_ref}/cancel?agreement_ref=TEST-VERIFY-AGENT-001`

5. **Verify:**
   - ✅ All booking operations succeed with test agreement
   - ✅ Booking operations fail if agreement is not verified
   - ✅ Agreement status changes to `ACTIVE` after successful verification
   - ✅ Test bookings are clearly marked as test data

---

## Agreement Flow Testing

### Background
Agreements connect Agents and Sources. Sources offer agreements, Agents approve them.

### Test 1: Source Offers Agreement to Agent

**Endpoint:** `POST /api/agreements/offer`

**Request (as Source):**
```json
{
  "agent_company_id": "<agent-company-id>",
  "agreement_ref": "AGR-SOURCE-A-001",
  "name": "Test Agreement",
  "terms": "Standard terms apply"
}
```

**Verify:**
- ✅ Agreement created with status `PENDING_APPROVAL`
- ✅ Email notification sent to Agent
- ✅ Dashboard notification created for Agent
- ✅ Agreement visible in Agent's pending agreements list

### Test 2: Agent Receives Notification

**Check Email:**
- ✅ Email sent to Agent's registered email
- ✅ Email contains agreement details
- ✅ Email contains link to approve/reject

**Check Dashboard:**
- ✅ Notification appears in Agent dashboard
- ✅ Notification shows agreement details
- ✅ Agent can click to view full agreement

### Test 3: Agent Approves Agreement

**Endpoint:** `POST /api/agreements/{agreement-id}/approve`

**Request (as Agent):**
```json
{
  "agreement_id": "<agreement-id>"
}
```

**Verify:**
- ✅ Agreement status changes to `ACTIVE`
- ✅ Email notification sent to Source confirming approval
- ✅ Agreement visible in both Agent and Source dashboards
- ✅ Agreement can now be used for availability/booking requests

### Test 4: Agent Rejects Agreement

**Endpoint:** `POST /api/agreements/{agreement-id}/reject`

**Request:**
```json
{
  "reason": "Terms not suitable"
}
```

**Verify:**
- ✅ Agreement status changes to `REJECTED`
- ✅ Email notification sent to Source
- ✅ Rejection reason is stored
- ✅ Agreement cannot be used for requests

### Test 5: Agreement Duplicate Prevention

**Test:** Source tries to create duplicate agreement_ref

**Verify:**
- ✅ Warning shown if agreement_ref already exists for that Source
- ✅ System prevents duplicates within same Source's agreements
- ✅ Different Sources can have same agreement_ref

---

## Location Management Testing

### Background
- System uses NATO LOCODES (UN/LOCODE)
- Sources expose locations endpoint
- Locations must match NATO LOCODES format
- System periodically checks supported locations per Source

### Test 1: Source Exposes Locations Endpoint

**gRPC:** `GetLocations(agreement_ref)`
**REST:** `GET /locations?agreement_ref=TEST-AGR-001`

**Expected Response:**
```json
{
  "locations": [
    {
      "locode": "GBMAN",
      "name": "Manchester, United Kingdom",
      "country": "GB"
    },
    {
      "locode": "GBGLA",
      "name": "Glasgow, United Kingdom",
      "country": "GB"
    }
  ]
}
```

**Verify:**
- ✅ All locodes are valid NATO LOCODES
- ✅ Format matches UN/LOCODE specification (5 characters: 2 country + 3 location)
- ✅ Agreement reference is included in request

### Test 2: Location Validation

**Test Invalid LOCODES:**
- `INVALID` (not a valid LOCODE)
- `GB` (too short)
- `GBMANCHESTER` (too long)
- Empty string

**Verify:**
- ✅ System validates LOCODE format
- ✅ Invalid LOCODES are rejected with clear error
- ✅ Valid LOCODES are accepted

### Test 3: Periodic Location Sync

**Test:** System checks locations per Source periodically

**Verify:**
- ✅ Locations are synced on schedule (e.g., daily)
- ✅ Location changes are detected
- ✅ Agreement location coverage is updated
- ✅ Agents see updated location lists

### Test 4: Agreement-Specific Locations

**Test:** Different agreements may have different location coverage

**Verify:**
- ✅ Agreement A may support `GBMAN`, `GBGLA`
- ✅ Agreement B may support `USNYC`, `USLAX`
- ✅ Location check includes agreement reference
- ✅ Availability requests respect agreement location coverage

### Test 5: Location Search

**Endpoint:** `GET /locations/search?q=manchester`

**Verify:**
- ✅ Search returns matching LOCODES
- ✅ Results include location names
- ✅ Search is case-insensitive
- ✅ Supports partial matches

---

## Availability Endpoint Testing

### Background
- First production-grade endpoint
- Transport: JSON (REST) and gRPC
- Data model follows OTA XML API specification (OTA_Veh*)
- Field names match OTA specification

### Test 1: Submit Availability Request (REST)

**Endpoint:** `POST /availability/submit`

**Request:**
```json
{
  "pickup_unlocode": "GBMAN",
  "dropoff_unlocode": "GBGLA",
  "pickup_iso": "2025-11-03T10:00:00Z",
  "dropoff_iso": "2025-11-05T10:00:00Z",
  "driver_age": 30,
  "residency_country": "GB",
  "vehicle_classes": ["ECAR", "ICAR"],
  "agreement_refs": ["TEST-AGR-001"],
  "rate_prefs": ["STANDARD"]
}
```

**Expected Response:**
```json
{
  "request_id": "avail_req_abc123",
  "status": "PENDING"
}
```

**Verify:**
- ✅ Request ID is generated
- ✅ Agreement references are validated
- ✅ Locations are validated (must be supported by agreement)
- ✅ Request is fanned out to all active sources for those agreements
- ✅ OTA field names are used (pickup_unlocode, dropoff_unlocode, etc.)

### Test 2: Poll Availability Results (Long Poll)

**Endpoint:** `GET /availability/poll?request_id=avail_req_abc123&since_seq=0&wait_ms=10000`

**Expected Response (Partial):**
```json
{
  "items": [
    {
      "offer_id": "offer_123",
      "supplier_id": "source1",
      "vehicle_class": "ECAR",
      "total_price": 150.00,
      "currency": "GBP",
      "pickup_location": "GBMAN",
      "dropoff_location": "GBGLA"
    }
  ],
  "status": "PARTIAL",
  "cursor": 1
}
```

**Expected Response (Complete):**
```json
{
  "items": [...],
  "status": "COMPLETE",
  "cursor": 5
}
```

**Verify:**
- ✅ Long polling works (waits up to 10 seconds)
- ✅ Partial results stream as sources respond
- ✅ Results aggregate from multiple sources
- ✅ Status changes to `COMPLETE` when all sources finish
- ✅ Cursor tracks sequence correctly

### Test 3: Availability with Multiple Agreements

**Test:** Agent has 3 active agreements

**Request:**
```json
{
  "agreement_refs": ["AGR-001", "AGR-002", "AGR-003"],
  ...
}
```

**Verify:**
- ✅ Request fans out to all 3 sources
- ✅ Results include offers from all sources
- ✅ Each offer identifies its source and agreement
- ✅ Results are deduplicated if same offer from multiple sources

### Test 4: Availability Timeout Handling

**Test:** One source responds slowly (>3s threshold)

**Verify:**
- ✅ Slow source gets strike after timeout
- ✅ After 3 strikes, source is excluded for 15 minutes
- ✅ Results finalized as `COMPLETE` even if source times out
- ✅ Health metrics track source performance

### Test 5: Availability with OTA Fields

**Verify OTA Field Mapping:**
- ✅ `pickup_unlocode` → OTA `PickupLocation/@LocationCode`
- ✅ `dropoff_unlocode` → OTA `DropOffLocation/@LocationCode`
- ✅ `pickup_iso` → OTA `@PickupDateTime`
- ✅ `dropoff_iso` → OTA `@ReturnDateTime`
- ✅ `driver_age` → OTA `DriverType/@Age`
- ✅ `vehicle_classes` → OTA `VehiclePrefs/VehiclePref/@VehicleClass`

### Test 6: Availability Error Handling

**Test Scenarios:**
1. Invalid agreement reference
2. Unsupported location
3. Invalid date format
4. Missing required fields
5. Source returns error

**Verify:**
- ✅ Appropriate error messages
- ✅ Errors don't crash the system
- ✅ Partial results still returned if some sources succeed

---

## Booking Operations Testing

### Background
- System acts as pass-through: ingest data, append agreement reference, forward to supplier
- Each booking request goes to a single supplier (unlike Availability)
- All booking operations must be logged

### Test 1: Create Booking

**Endpoint:** `POST /bookings`

**Request:**
```json
{
  "agreement_ref": "TEST-AGR-001",
  "supplier_id": "source1",
  "offer_id": "offer_123",
  "supplier_offer_ref": "SUPPLIER-OFFER-456",
  "agent_booking_ref": "AGENT-BKG-789",
  "driver": {
    "first_name": "John",
    "last_name": "Doe",
    "email": "john.doe@example.com",
    "phone": "+44 20 1234 5678",
    "age": 30
  }
}
```

**Headers:**
```
Idempotency-Key: unique-key-12345
Authorization: Bearer <token>
```

**Expected Response:**
```json
{
  "supplier_booking_ref": "BKG-SOURCE1-ABC123",
  "status": "CONFIRMED",
  "agreement_ref": "TEST-AGR-001",
  "created_at": "2025-10-29T10:00:00Z"
}
```

**Verify:**
- ✅ Idempotency key is required
- ✅ Same idempotency key returns same booking (idempotent)
- ✅ Agreement reference is appended to request
- ✅ Request is forwarded to correct source
- ✅ Response is logged (request + response)
- ✅ Booking is stored in database

### Test 2: Modify Booking

**Endpoint:** `PATCH /bookings/{supplier_booking_ref}?agreement_ref=TEST-AGR-001`

**Request:**
```json
{
  "driver": {
    "email": "newemail@example.com",
    "phone": "+44 20 8765 4321"
  }
}
```

**Verify:**
- ✅ Only specified fields are updated
- ✅ Agreement reference is validated
- ✅ Request forwarded to source
- ✅ Request and response logged

### Test 3: Cancel Booking

**Endpoint:** `POST /bookings/{supplier_booking_ref}/cancel?agreement_ref=TEST-AGR-001`

**Verify:**
- ✅ Booking status changes to `CANCELLED`
- ✅ Cancellation forwarded to source
- ✅ Request and response logged
- ✅ Cannot modify/cancel already cancelled booking

### Test 4: Check Booking Status

**Endpoint:** `GET /bookings/{supplier_booking_ref}?agreement_ref=TEST-AGR-001`

**Expected Response:**
```json
{
  "supplier_booking_ref": "BKG-SOURCE1-ABC123",
  "status": "CONFIRMED",
  "agreement_ref": "TEST-AGR-001",
  "driver": {...},
  "pickup_date": "2025-11-03T10:00:00Z",
  "dropoff_date": "2025-11-05T10:00:00Z"
}
```

**Verify:**
- ✅ Returns current booking status
- ✅ Includes all booking details
- ✅ Agreement reference validated
- ✅ Request logged

### Test 5: Booking Logging

**Verify Logs:**
- ✅ Every booking request is logged with:
  - Request timestamp
  - Request payload
  - Agreement reference
  - Agent/Source IDs
  - Request ID
- ✅ Every booking response is logged with:
  - Response timestamp
  - Response payload
  - Response status
  - Duration

**Check Log Storage:**
```bash
# Check logs table or log files
SELECT * FROM booking_logs WHERE booking_ref = 'BKG-123' ORDER BY created_at;
```

---

## Admin Panel Testing

### Test 1: Dashboard Overview

**Verify:**
- ✅ Shows total agents, sources, agreements
- ✅ Shows active bookings
- ✅ Shows system health metrics
- ✅ Shows recent activity

### Test 2: Source Management

**CRUD Operations:**
- ✅ Create new source
- ✅ View source details
- ✅ Update source configuration
- ✅ Delete source (with proper validation)

**Health Management:**
- ✅ View source health status
- ✅ Reset source health (clear strikes)
- ✅ View source performance metrics
- ✅ Exclude/include sources manually

### Test 3: Agreement Management

**CRUD Operations:**
- ✅ Create agreements
- ✅ View all agreements
- ✅ Update agreement details
- ✅ Delete agreements

**Agreement Status:**
- ✅ Filter by status (PENDING, ACTIVE, REJECTED, EXPIRED)
- ✅ View agreement history
- ✅ See which agent/source owns agreement

### Test 4: Diagnostic Tools

**Echo Test:**
- ✅ Submit echo from admin panel
- ✅ View echo results in real-time
- ✅ See which sources responded

**Endpoint Testing:**
- ✅ Test source gRPC endpoint connectivity
- ✅ Test HTTP endpoint connectivity
- ✅ View test results with response times

### Test 5: Audit Logs

**Verify:**
- ✅ All operations are logged
- ✅ Logs include user, action, timestamp
- ✅ Logs are searchable
- ✅ Logs are exportable

---

## Source Health & Performance Testing

### Test 1: Source Response Time Tracking

**Test:** Submit multiple availability requests

**Verify:**
- ✅ Response times are tracked per source
- ✅ Average response time calculated
- ✅ Slow responses (>3s) are flagged

### Test 2: Strike System

**Test:** Source responds slowly 3 times consecutively

**Verify:**
- ✅ After 1st strike: Warning logged
- ✅ After 2nd strike: Warning logged
- ✅ After 3rd strike: Source excluded for 15 minutes
- ✅ Exclusion time increases: 15min → 30min → 60min

### Test 3: Source Auto-Heal

**Test:** Source is excluded, then responds quickly

**Verify:**
- ✅ After backoff period, source is re-included
- ✅ Health status resets to healthy if responses improve
- ✅ Admin can manually reset health

### Test 4: Health Metrics Dashboard

**Verify Metrics:**
- ✅ Response time percentiles (p50, p95, p99)
- ✅ Success rate
- ✅ Error rate
- ✅ Exclusion status
- ✅ Last successful response time

---

## SDK Testing

### Test 1: Node.js/TypeScript SDK

**Install:**
```bash
cd gloriaconnect_backend/sdks/nodejs-agent
npm install
npm run build
```

**Test Script:**
```typescript
import { CarHireClient, Config } from './src';

const config = Config.forRest({
  baseUrl: 'http://localhost:8080',
  token: 'Bearer <token>',
  agentId: 'test-agent'
});

const client = new CarHireClient(config);

// Test availability
const criteria = AvailabilityCriteria.make({
  pickupLocode: 'GBMAN',
  returnLocode: 'GBGLA',
  pickupAt: new Date('2025-11-03T10:00:00Z'),
  returnAt: new Date('2025-11-05T10:00:00Z'),
  driverAge: 30,
  currency: 'GBP',
  agreementRefs: ['TEST-AGR-001']
});

for await (const chunk of client.getAvailability().search(criteria)) {
  console.log(`Status: ${chunk.status}, Items: ${chunk.items.length}`);
  if (chunk.status === 'COMPLETE') break;
}
```

**Verify:**
- ✅ SDK connects to middleware
- ✅ Availability search works
- ✅ Booking operations work
- ✅ Error handling works

### Test 2: PHP SDK

**Install:**
```bash
cd gloriaconnect_backend/sdks/php-agent
composer install
```

**Test Script:**
```php
<?php
require 'vendor/autoload.php';

use HMS\CarHire\CarHireClient;
use HMS\CarHire\Config;

$config = Config::forRest([
    'baseUrl' => 'http://localhost:8080',
    'token' => 'Bearer <token>',
    'agentId' => 'test-agent'
]);

$client = new CarHireClient($config);

// Test availability
$criteria = [
    'pickupLocode' => 'GBMAN',
    'returnLocode' => 'GBGLA',
    // ...
];

$stream = $client->availability()->search($criteria);
foreach ($stream as $chunk) {
    echo "Status: {$chunk->status}\n";
    if ($chunk->status === 'COMPLETE') break;
}
```

### Test 3: Python SDK

**Install:**
```bash
cd gloriaconnect_backend/sdks/python-agent
pip install -e .
```

**Test Script:**
```python
from carhire import CarHireClient, Config

config = Config.for_rest({
    'baseUrl': 'http://localhost:8080',
    'token': 'Bearer <token>',
    'agentId': 'test-agent'
})

client = CarHireClient(config)

# Test availability
async for chunk in client.get_availability().search(criteria):
    print(f"Status: {chunk.status}")
    if chunk.status == 'COMPLETE':
        break
```

### Test 4: Go SDK

**Test:** Follow README examples in `gloriaconnect_backend/sdks/go-agent/README.md`

### Test 5: Java SDK

**Test:** Follow README examples in `gloriaconnect_backend/sdks/java-agent/README.md`

---

## Load Testing & Scaling

### Test 1: Light Load

**Configuration:**
- Concurrent requests: 5
- Requests per second: 2
- Duration: 30 seconds

**Run:**
```bash
npm run tsx scripts/load-test.ts
```

**Verify:**
- ✅ All requests succeed
- ✅ Average response time < 500ms
- ✅ No errors
- ✅ Memory stable

### Test 2: Medium Load

**Configuration:**
- Concurrent requests: 10
- Requests per second: 5
- Duration: 60 seconds

**Verify:**
- ✅ System handles load
- ✅ Response times acceptable
- ✅ Error rate < 1%

### Test 3: Heavy Load

**Configuration:**
- Concurrent requests: 50
- Requests per second: 20
- Duration: 120 seconds

**Monitor:**
- CPU usage
- Memory usage
- Database connections
- Response times
- Error rates

**Verify:**
- ✅ System remains stable
- ✅ No memory leaks
- ✅ Database connection pool handles load
- ✅ Response times degrade gracefully

### Test 4: Port Usage Under Load

**Monitor:** (As requested by client)
- How port usage scales
- Connection count
- Resource usage per connection

**Tools:**
```bash
# Linux
netstat -an | grep :8080 | wc -l
ss -tuln | grep :8080

# Monitor with Prometheus
curl http://localhost:8080/metrics
```

---

## End-to-End Integration Testing

### Test Scenario 1: Complete Booking Flow

1. **Agent Registers** → ✅ Account created
2. **Source Registers** → ✅ Account created
3. **Source Creates Agreement** → ✅ Agreement pending
4. **Agent Approves Agreement** → ✅ Agreement active
5. **Agent Searches Availability** → ✅ Results from source
6. **Agent Creates Booking** → ✅ Booking confirmed
7. **Agent Modifies Booking** → ✅ Booking updated
8. **Agent Checks Booking** → ✅ Status correct
9. **Agent Cancels Booking** → ✅ Booking cancelled

### Test Scenario 2: Multiple Agents, Multiple Sources

1. Create 2 agents, 3 sources
2. Create 6 agreements (2 per agent, 1 per source)
3. All agents search availability simultaneously
4. Verify:
   - ✅ Results are isolated per agent
   - ✅ No cross-contamination
   - ✅ All sources respond
   - ✅ System handles concurrent load

### Test Scenario 3: Source Degradation

1. One source starts responding slowly
2. System excludes source after 3 strikes
3. Other sources continue working
4. Excluded source heals after backoff
5. Verify:
   - ✅ Health tracking works
   - ✅ Exclusion works
   - ✅ Auto-heal works
   - ✅ Other sources unaffected

---

## Pre-Delivery Checklist

### Functional Requirements

- [ ] Registration works for Agents and Sources
- [ ] Authentication and authorization work
- [ ] Echo/Ping API works (submit → poll → complete)
- [ ] Source verification (automated API tests) works
- [ ] Agent verification (manual booking tests) works
- [ ] Agreement flow works (offer → approve → active)
- [ ] Location management works (NATO LOCODES)
- [ ] Availability endpoint works (OTA XML field names)
- [ ] Booking operations work (Create, Modify, Cancel, Check)
- [ ] All booking operations are logged
- [ ] Admin panel functions work
- [ ] Source health tracking works
- [ ] Strike system works (3 strikes → exclusion)
- [ ] Auto-heal works

### Performance Requirements

- [ ] Availability submit: < 200ms
- [ ] Availability poll: < 100ms (when ready)
- [ ] Booking create: < 300ms
- [ ] Booking check: < 150ms
- [ ] System handles 100+ requests/second
- [ ] System supports 1000+ concurrent connections
- [ ] Memory usage stable under load
- [ ] Database query times < 100ms

### SDK Requirements

- [ ] Node.js SDK complete and tested
- [ ] PHP SDK complete and tested
- [ ] Python SDK complete and tested
- [ ] Java SDK complete and tested
- [ ] Perl SDK complete and tested
- [ ] Go SDK complete (gRPC stub acceptable)

### Documentation

- [ ] API documentation complete
- [ ] SDK documentation complete
- [ ] Setup/installation guide complete
- [ ] Testing guide complete (this document)
- [ ] Deployment guide complete

### Security

- [ ] Authentication secure (JWT)
- [ ] Passwords hashed
- [ ] API rate limiting works
- [ ] CORS configured correctly
- [ ] Input validation on all endpoints
- [ ] SQL injection prevention
- [ ] XSS prevention

### Logging & Monitoring

- [ ] Structured logging in place
- [ ] All booking operations logged
- [ ] Prometheus metrics exposed
- [ ] Health endpoints working
- [ ] Error tracking in place

### Database

- [ ] Migrations run successfully
- [ ] Test data seeded
- [ ] Indexes optimized
- [ ] Backup strategy in place

### Deployment

- [ ] Environment variables documented
- [ ] Docker setup (if applicable)
- [ ] Server setup guide complete
- [ ] Database setup guide complete

---

## Quick Test Commands Reference

```bash
# Health Check
curl http://localhost:8080/health

# Login
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"agent1@test.com","password":"agent123"}'

# Submit Availability (with token)
curl -X POST http://localhost:8080/availability/submit \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{...}'

# Poll Availability
curl "http://localhost:8080/availability/poll?request_id=req_123&since_seq=0&wait_ms=10000" \
  -H "Authorization: Bearer <token>"

# Create Booking
curl -X POST http://localhost:8080/bookings \
  -H "Authorization: Bearer <token>" \
  -H "Idempotency-Key: unique-key-123" \
  -H "Content-Type: application/json" \
  -d '{...}'

# Check Metrics
curl http://localhost:8080/metrics
```

---

## Troubleshooting

### Common Issues

1. **Database Connection Fails**
   - Check `.env` `DATABASE_URL`
   - Verify MySQL is running
   - Test connection: `npm run test:db`

2. **Port Already in Use**
   - Change `PORT` in `.env`
   - Or stop conflicting service

3. **gRPC Connection Fails**
   - Verify source backend is running
   - Check gRPC port (default 50051, 50052)
   - Verify mTLS certificates (if enabled)

4. **Tests Fail**
   - Ensure test data is seeded
   - Check authentication tokens
   - Verify agreements are ACTIVE

---

## Next Steps After Testing

1. **Fix any issues found**
2. **Document test results**
3. **Prepare deployment package**
4. **Share with client for review**
5. **Be ready for client feedback and iterations**

---

**Last Updated:** Based on client conversations and requirements as of October 2025
**Version:** 1.0

