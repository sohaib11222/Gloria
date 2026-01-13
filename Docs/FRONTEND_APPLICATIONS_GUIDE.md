# Frontend Applications Complete User Guide

## Overview

This guide covers all three frontend applications built for the Gloria Connect Car Hire Middleware:
1. **Admin Panel** - System administration and management
2. **Agent Panel** - For Online Travel Agencies (OTAs)
3. **Source Panel** - For car rental suppliers

---

## Table of Contents

1. [Installation & Setup](#installation--setup)
2. [Admin Panel Guide](#admin-panel-guide)
3. [Agent Panel Guide](#agent-panel-guide)
4. [Source Panel Guide](#source-panel-guide)
5. [Common Features](#common-features)
6. [Troubleshooting](#troubleshooting)

---

## Installation & Setup

### Prerequisites

- **Node.js** 18+ 
- **npm** or **yarn**
- **Backend middleware** running on `http://localhost:8080` (or configured port)

### Quick Start

#### 1. Admin Panel

```bash
cd gloriaconnect_admin
npm install
npm run dev
# Opens at http://localhost:5173
```

#### 2. Agent Panel

```bash
cd gloriaconnect_agent
npm install
npm run dev
# Opens at http://localhost:5174 (or next available port)
```

#### 3. Source Panel

```bash
cd gloriaconnect_source
npm install
npm run dev
# Opens at http://localhost:5175 (or next available port)
```

### Environment Configuration

Each application uses environment variables. Create `.env` file in each project root:

**Admin Panel** (`.env`):
```env
VITE_MIDDLEWARE_URL=http://localhost:8080
VITE_APP_NAME=Gloria Connect Admin
```

**Agent Panel** (`.env`):
```env
VITE_API_BASE_URL=http://localhost:8080
```

**Source Panel** (`.env`):
```env
VITE_API_BASE_URL=http://localhost:8080
```

**Note:** In production with reverse proxy, you can leave `VITE_API_BASE_URL` empty to use relative paths.

---

## Admin Panel Guide

### Access

**URL:** `http://localhost:5173`

**Login:**
- Email: `admin@test.com` (or your admin email)
- Password: `admin123` (or your admin password)

### Main Navigation

The admin panel includes the following sections accessible from the sidebar:

#### Dashboard (`/dashboard`)
**Purpose:** System overview and KPIs

**Features:**
- Total number of Sources, Agents, and Agreements
- Number of excluded sources (health issues)
- Recent verification results
- System health overview
- Quick action buttons for common tasks

**How to Use:**
1. View system statistics at a glance
2. Monitor recent activity
3. Access quick actions for common tasks
4. Check system health status

#### Companies (`/companies`)
**Purpose:** Manage all registered companies

**Features:**
- List all companies (Agents and Sources)
- Filter by type (AGENT/SOURCE)
- View company details
- Edit company information
- View company agreements

**Actions Available:**
- **View Details:** Click on any company to see full information
- **Edit:** Update company settings, API endpoints
- **View Agreements:** See all agreements associated with company

#### Agents (`/agents`)
**Purpose:** Manage agent companies specifically

**Features:**
- List all agent companies
- View agent verification status
- Offer agreements to agents (on behalf of sources)
- View agent activity

#### Sources (`/sources`)
**Purpose:** Manage source companies and their health

**Features:**
- List all source companies
- Edit source API endpoints (HTTP/gRPC)
- Run health checks
- Reset health data
- Sync locations
- Manage IP whitelist
- View source performance metrics

**Actions Available:**
- **Edit Endpoints:** Update gRPC or HTTP endpoint URLs
- **Test Connection:** Test connectivity to source endpoints
- **Reset Health:** Clear health strikes and exclusion status
- **Sync Locations:** Trigger location synchronization
- **View Metrics:** See response times and success rates

#### Agreements Management (`/agreements-management`)
**Purpose:** Comprehensive agreement management

**Features:**
- List all agreements in the system
- Filter by status (PENDING, ACTIVE, REJECTED, EXPIRED)
- View agreement details
- Accept/reject agreements on behalf of parties
- Create new agreements
- Search agreements

**Agreement Statuses:**
- **PENDING_APPROVAL:** Waiting for agent approval
- **ACTIVE:** Agreement is active and can be used
- **REJECTED:** Agent rejected the agreement
- **EXPIRED:** Agreement has expired

#### Agreements (`/agreements`)
**Purpose:** Simplified agreement view and quick actions

**Features:**
- Quick view of agreements
- Accept/reject agreements
- View agreement history

#### Registration (`/registration`)
**Purpose:** Register new companies

**Features:**
- Register new Agents
- Register new Sources
- Set company details (name, email, password)
- Configure initial settings

#### Connectivity (`/connectivity`)
**Purpose:** Test and manage system connectivity

**Features:**
- Test backend connectivity
- View connection status
- Check API endpoint availability
- View system logs related to connectivity

#### Agent Page (`/agent`)
**Purpose:** Agent-specific operations and management

**Features:**
- View agent information
- Manage agent endpoints
- View agent agreements
- Test agent connectivity

#### Locations (`/locations`)
**Purpose:** Manage location data

**Features:**
- View all locations in system
- Search locations by LOCODE
- View location details
- Manage location coverage per agreement

#### Branches (`/branches`)
**Purpose:** Manage rental branches/locations

**Features:**
- List all branches
- Create/edit/delete branches
- Assign branches to sources
- View branch details

#### Location Requests (`/location-requests`)
**Purpose:** Handle location addition requests

**Features:**
- View pending location requests
- Approve/reject location requests
- View request history

#### UN/LOCODEs (`/unlocodes`)
**Purpose:** Manage NATO LOCODE database

**Features:**
- Search UN/LOCODE database
- View LOCODE details
- Validate LOCODEs
- Import/export LOCODE data

#### Location Validation (`/location-validation`)
**Purpose:** Validate locations against agreements

**Features:**
- Test location support per agreement
- Validate LOCODE format
- Check location coverage

#### Availability Tester (`/availability`)
**Purpose:** Test availability endpoint functionality

**Features:**
- Submit availability requests
- Real-time polling for results
- Display incremental offers as they arrive
- View raw JSON responses
- Copy responses for debugging

**How to Use:**
1. Fill in search criteria:
   - Pickup location (UN/LOCODE)
   - Return location (UN/LOCODE)
   - Pickup date/time
   - Return date/time
   - Driver age
   - Currency
   - Agreement references
2. Click "Submit Request"
3. Watch results stream in real-time
4. Results show as PARTIAL, then COMPLETE when all sources respond

#### Bookings Tester (`/bookings-test`)
**Purpose:** Test booking operations

**Features:**
- Create bookings with idempotency keys
- Modify existing bookings
- Cancel bookings
- Check booking status
- View booking history

**How to Use:**
1. **Create Booking:**
   - Enter agreement reference
   - Enter supplier ID and offer ID
   - Add driver information
   - Provide idempotency key (required)
   - Submit

2. **Modify Booking:**
   - Enter supplier booking reference
   - Select fields to modify
   - Submit changes

3. **Cancel Booking:**
   - Enter supplier booking reference
   - Confirm cancellation

4. **Check Booking:**
   - Enter supplier booking reference
   - View current status

#### Verification (`/verification`)
**Purpose:** Run verification tests for sources and agents

**Features:**
- Run source verification (automated API tests)
- Run agent verification (manual booking tests)
- View step-by-step test results
- Monitor test latencies
- View verification history

**Source Verification:**
- Tests Health endpoint
- Tests Locations endpoint
- Tests Availability endpoint
- Tests Booking endpoints
- Verifies response formats
- Checks response times

**Agent Verification:**
- Tests booking creation with test data
- Tests booking modification
- Tests booking cancellation
- Verifies integration works correctly

#### Health (`/health`)
**Purpose:** Monitor source health and performance

**Features:**
- View source health metrics
- See slow response rates
- View sample counts
- Manage backoff levels
- Reset health data for sources
- View exclusion status

**Metrics Displayed:**
- Response time percentiles (p50, p95, p99)
- Success rate
- Error rate
- Number of strikes
- Current backoff level
- Exclusion status

#### Metrics (`/metrics`)
**Purpose:** View Prometheus metrics and system performance

**Features:**
- View all Prometheus metrics
- Real-time charts for latency and duration
- Configurable refresh intervals
- Parse histogram data
- Export metrics data

#### Booking Logs (`/booking-logs`)
**Purpose:** View booking operation logs

**Features:**
- Browse booking creation logs
- View booking modification logs
- See booking cancellation logs
- Filter by date, company, agreement
- View request/response payloads
- Export logs

#### System Logs (`/logs`)
**Purpose:** Browse system logs and debugging information

**Features:**
- Filter by log level (INFO, WARN, ERROR)
- Filter by company, source, request ID
- View request IDs and endpoints
- Search logs
- Export log data
- Graceful fallback if logs API unavailable

#### Activity (`/activity`)
**Purpose:** View system activity and audit trail

**Features:**
- View all system activity
- See user actions
- Track agreement changes
- View verification activities
- Filter by date, user, action type

#### Integrations (`/integrations`)
**Purpose:** Manage system integrations

**Features:**
- View integration status
- Configure integration settings
- Test integrations
- View integration logs

#### IP Whitelist (`/ip-whitelist`)
**Purpose:** Manage IP address whitelist

**Features:**
- Add IP addresses to whitelist
- Remove IP addresses
- Test whitelist access
- View whitelist entries

#### Support (`/support`)
**Purpose:** Access support resources

**Features:**
- Contact support
- View help documentation
- Submit support tickets
- Access FAQs

#### API Reference (`/docs`)
**Purpose:** Interactive API documentation

**Features:**
- Browse all API endpoints
- View request/response examples
- Test endpoints directly from docs
- View SDK guides
- Copy code samples

**Available Documentation:**
- Authentication endpoints
- Availability endpoints
- Booking endpoints
- Agreement endpoints
- Location endpoints
- Verification endpoints
- Admin endpoints

#### Changelog (`/changelog`)
**Purpose:** View recent changes and updates

**Features:**
- See latest features
- View bug fixes
- Read update notes
- Version history

#### Settings (`/settings`)
**Purpose:** System configuration and preferences

**Features:**
- System configuration toggles
- Backend information
- Environment details
- API base URL display
- User preferences

### Search Functionality

Press `Ctrl+K` (or `Cmd+K` on Mac) to open the search palette:
- Search for pages and features
- Quick navigation
- Keyboard shortcuts

### Notifications

Click the bell icon in the top bar to view:
- New agreement offers
- Verification results
- System alerts
- Important updates

---

## Agent Panel Guide

### Access

**URL:** `http://localhost:5174` (or configured port)

**Registration:**
1. Go to `/register`
2. Fill in company details:
   - Email
   - Company name
   - Password
   - Company type: AGENT
3. Submit registration
4. Verify email (if email verification enabled)

**Login:**
- Email: Your registered email
- Password: Your password

### Main Pages

#### Agent Dashboard (`/agent`)
**Purpose:** Main agent information and quick actions

**Sections:**

1. **Agent Information**
   - Company name and email
   - Agent ID
   - Verification status
   - Registration date

2. **Verification Status**
   - Shows if agent is verified
   - Verification requirements:
     - Create test booking
     - Modify test booking
     - Cancel test booking
   - Start verification button

3. **Endpoint Configuration**
   - View/configure API endpoints
   - Test connectivity
   - Update endpoint URLs

4. **Agreement Offers**
   - View pending agreement offers from sources
   - Accept/reject offers
   - View agreement details
   - See notification count

5. **Quick Actions**
   - Start Verification
   - View Agreements
   - Test Availability
   - View Bookings

**Status Badges:**
- ✅ **Verified:** Can access all features
- ⏳ **Pending Verification:** Limited access, must complete verification
- ❌ **Not Verified:** Cannot use agreements

#### Availability Test (`/availability`)
**Purpose:** Search for car rental availability

**Features:**
- Submit availability search requests
- Real-time polling for results
- Display offers as they arrive
- View offer details (vehicle class, price, supplier)
- Filter and sort results
- Export results

**How to Use:**

1. **Enter Search Criteria:**
   - **Pickup Location:** Enter UN/LOCODE (e.g., GBMAN for Manchester)
   - **Return Location:** Enter UN/LOCODE
   - **Pickup Date/Time:** Select date and time
   - **Return Date/Time:** Select date and time
   - **Driver Age:** Enter driver age
   - **Currency:** Select currency (e.g., GBP, USD)
   - **Agreements:** Select one or more active agreements

2. **Optional Fields:**
   - Vehicle preferences (classes)
   - Rate preferences
   - Residency country

3. **Submit Request:**
   - Click "Search Availability"
   - System assigns request ID
   - Results stream in real-time

4. **View Results:**
   - Results appear as they arrive from sources
   - Status shows "PARTIAL" while waiting
   - Status changes to "COMPLETE" when all sources respond
   - Each offer shows:
     - Supplier name
     - Vehicle class
     - Total price
     - Currency
     - Pickup/dropoff locations

5. **Create Booking:**
   - Click on an offer
   - Fill in driver details
   - Create booking

#### Agreements (`/agreements`)
**Purpose:** Manage agreements with sources

**Features:**
- View all agreements (active, pending, rejected)
- Accept/reject agreement offers
- View agreement details
- See agreement history

**Agreement States:**
- **PENDING:** Waiting for your approval
- **ACTIVE:** Agreement is active, can be used for searches
- **REJECTED:** You rejected the offer

**Actions:**
- **Accept:** Click "Accept" on pending offers
- **View Details:** Click agreement to see full terms
- **Filter:** Filter by status

#### My Bookings (`/bookings`)
**Purpose:** View and manage bookings

**Features:**
- List all your bookings
- Filter by status (CONFIRMED, CANCELLED, etc.)
- View booking details
- Modify bookings
- Cancel bookings
- Check booking status
- Search bookings

**Booking Operations:**

1. **View Booking:**
   - Click on booking to see details
   - View driver information
   - See pickup/dropoff details
   - Check current status

2. **Modify Booking:**
   - Select booking
   - Click "Modify"
   - Update fields (driver email, phone, etc.)
   - Submit changes

3. **Cancel Booking:**
   - Select booking
   - Click "Cancel"
   - Confirm cancellation
   - Booking status changes to CANCELLED

#### Location Browser (`/locations`)
**Purpose:** Browse available locations

**Features:**
- Search locations by name or LOCODE
- View location details
- Check which agreements support locations
- View location coverage map

#### Support (`/support`)
**Purpose:** Get help and support

**Features:**
- Contact support
- View help documentation
- Submit support requests
- Access FAQs

#### API Documentation (`/docs`)
**Purpose:** Access API reference and SDK guides

**Features:**
- View API endpoint documentation
- Access SDK guides for various languages
- View code examples
- Copy integration code

### Notifications

Click the bell icon to see:
- New agreement offers
- Booking confirmations
- Verification results
- Important updates

---

## Source Panel Guide

### Access

**URL:** `http://localhost:5175` (or configured port)

**Registration:**
1. Go to `/register`
2. Fill in company details:
   - Email
   - Company name
   - Password
   - Company type: SOURCE
3. Submit registration
4. Verify email (if email verification enabled)

**Login:**
- Email: Your registered email
- Password: Your password

### Main Dashboard (`/source`)

The source panel uses a tabbed interface. Access different sections via tabs:

#### Dashboard Tab

**Purpose:** Overview and quick actions

**Features:**
- Source information display
- Verification status
- Agreement count
- Location count
- Recent activity
- Quick action buttons

#### Agreements Tab

**Purpose:** Manage agreements with agents

**Features:**

1. **My Agreements**
   - View all your agreements
   - See agreement status (ACTIVE, PENDING, etc.)
   - View which agents have agreements
   - See agreement details

2. **Available Agents**
   - List all registered agents
   - Offer agreements to agents
   - View agent details

3. **Create Agreement**
   - Form to create new agreement offer
   - Enter agreement reference (must be unique for your company)
   - Select agent to offer to
   - Enter agreement name and terms
   - Submit offer

4. **Agreement Details**
   - Click agreement to view full details
   - See agent information
   - View agreement status
   - See location coverage

**Agreement Status:**
- **PENDING_APPROVAL:** Waiting for agent to accept
- **ACTIVE:** Agent accepted, agreement is active
- **REJECTED:** Agent rejected the offer

#### Locations Tab

**Purpose:** Manage available locations

**Features:**

1. **Available Locations**
   - View all locations you support
   - Filter by agreement
   - Add new locations
   - Remove locations
   - View location details

2. **Add Location**
   - Enter UN/LOCODE (must be valid NATO LOCODE)
   - Enter location name
   - Select agreements this location applies to
   - Submit

3. **Location Requests**
   - View requests from agents for new locations
   - Approve/reject location requests

**Location Requirements:**
- Must be valid NATO LOCODE format (5 characters: 2 country + 3 location)
- Examples: GBMAN (Manchester, UK), USNYC (New York, USA)

#### Branches Tab

**Purpose:** Manage rental branches

**Features:**

1. **Branch List**
   - View all your branches
   - Edit branch information
   - Delete branches
   - Upload branches (CSV import)

2. **Create Branch**
   - Enter branch name
   - Select location (UN/LOCODE)
   - Enter address
   - Add contact information
   - Submit

3. **Edit Branch**
   - Click on branch to edit
   - Update information
   - Save changes

4. **Upload Branches**
   - Upload CSV file with branch data
   - System validates and imports branches
   - View import results

#### Location Requests Tab

**Purpose:** Handle location addition requests

**Features:**
- View pending location requests from agents
- Approve/reject requests
- View request history
- See which agents requested locations

#### Health Tab

**Purpose:** Monitor your source performance

**Features:**
- View response time metrics
- See success/error rates
- Monitor health status
- View exclusion status (if any)
- Check performance trends

**Metrics Displayed:**
- Average response time
- P95/P99 response times
- Success rate
- Error rate
- Current health status

#### Verification Tab

**Purpose:** Run and view verification tests

**Features:**

1. **Pending Verification**
   - Shows if verification is required
   - Start verification process

2. **Run Verification**
   - System runs automated tests:
     - Health endpoint test
     - Locations endpoint test
     - Availability endpoint test
     - Booking endpoints test
   - View test results in real-time
   - See which tests pass/fail

3. **Verification Results**
   - View detailed test results
   - See response times for each test
   - Identify any failures
   - Retry failed tests

**Verification Requirements:**
- All endpoints must respond correctly
- Response times must be < 3 seconds
- All required endpoints must be implemented

#### Support Tab

**Purpose:** Get help and support

**Features:**
- Contact support
- View help documentation
- Submit support requests
- Access FAQs

#### Docs Tab

**Purpose:** Access API reference and integration guides

**Features:**
- View API endpoint documentation
- Access SDK guides
- View integration examples
- Copy code samples
- Fullscreen documentation view

#### Settings Tab

**Purpose:** Configure source settings

**Features:**
- Update company information
- Change password
- Configure API endpoints
- View system information

### Endpoint Configuration

**Critical for Sources:** You must configure your gRPC endpoint

**Steps:**

1. Go to **Dashboard** tab
2. Find **Endpoint Configuration** section
3. Enter your gRPC endpoint address (e.g., `localhost:50052`)
4. Click **Test Connection** to verify
5. Save configuration

**Requirements:**
- gRPC endpoint must be accessible
- Must implement all required service methods:
  - `Health` check
  - `GetLocations`
  - `SubmitAvailability`
  - `CreateBooking`
  - `ModifyBooking`
  - `CancelBooking`
  - `CheckBooking`

### Verification Process

**Step 1: Configure Endpoint**
- Enter gRPC endpoint address
- Test connection (must succeed)

**Step 2: Run Verification**
- Go to Verification tab
- Click "Start Verification"
- System tests all endpoints
- Wait for results

**Step 3: View Results**
- All tests must pass
- Response times must be acceptable
- Fix any failures
- Retry if needed

**Step 4: Verification Complete**
- Once verified, you can:
  - Create agreements
  - Offer agreements to agents
  - Receive availability requests
  - Receive booking requests

---

## Common Features

### Authentication

All three applications use JWT token authentication:

1. **Login:**
   - Enter email and password
   - System returns JWT token
   - Token stored in localStorage
   - Token included in all API requests

2. **Session Management:**
   - Tokens expire after configured time
   - Automatic redirect to login when token expires
   - "Remember me" functionality (if implemented)

3. **Logout:**
   - Click logout button
   - Token is cleared
   - Redirected to login page

### Notifications

All applications include notification system:

- **Bell Icon:** Shows notification count
- **Notification Drawer:** Click bell to view notifications
- **Types:**
  - Agreement offers
  - Verification results
  - Booking confirmations
  - System alerts

### API Configuration

All applications support flexible API configuration:

**Development (default):**
- Uses `http://localhost:8080`

**Production:**
- Can use relative paths (empty base URL)
- Or configure custom API URL via environment variable

**See:** `API_CONFIG.md` in each application for details

### Error Handling

All applications include comprehensive error handling:

- **Network Errors:** Automatic retry with exponential backoff
- **Authentication Errors:** Automatic redirect to login
- **API Errors:** User-friendly error messages via toast notifications
- **Missing Endpoints:** Graceful fallbacks

### Responsive Design

All applications are responsive and work on:
- Desktop browsers
- Tablets
- Mobile devices

### Keyboard Shortcuts

**Common Shortcuts:**
- `Ctrl+K` (or `Cmd+K`): Open search palette
- `Esc`: Close modals/drawers
- `Ctrl+Enter`: Submit forms (where applicable)

---

## Troubleshooting

### Cannot Login

**Symptoms:** Login fails or redirects immediately

**Solutions:**
1. Verify backend is running on correct port
2. Check API base URL configuration
3. Verify credentials are correct
4. Check browser console for errors
5. Clear browser cache and localStorage
6. Check CORS configuration on backend

### API Calls Failing

**Symptoms:** "Network Error" or "Failed to fetch"

**Solutions:**
1. Verify backend is running
2. Check API base URL in Settings
3. Verify CORS is configured on backend
4. Check network tab in browser DevTools
5. Verify authentication token is valid

### Notifications Not Showing

**Symptoms:** Bell icon shows count but no notifications appear

**Solutions:**
1. Refresh the page
2. Check browser console for errors
3. Verify notifications API endpoint is working
4. Clear browser cache

### Pages Not Loading

**Symptoms:** Blank page or 404 errors

**Solutions:**
1. Verify you're logged in
2. Check browser console for errors
3. Verify route exists in application
4. Try refreshing the page
5. Clear browser cache

### Build Errors

**Symptoms:** `npm run build` fails

**Solutions:**
1. Ensure Node.js 18+ is installed
2. Delete `node_modules` and `package-lock.json`
3. Run `npm install` again
4. Check for TypeScript errors
5. Verify all environment variables are set

### Production Deployment Issues

**Symptoms:** Works locally but not in production

**Solutions:**
1. Verify API base URL is configured correctly
2. Check reverse proxy configuration
3. Verify environment variables are set in production
4. Check browser console for errors
5. Verify CORS allows production domain
6. Check server logs for errors

---

## Quick Reference

### Admin Panel URLs
- Login: `/login`
- Dashboard: `/dashboard`
- Sources: `/sources`
- Agents: `/agents`
- Agreements: `/agreements-management`
- Availability Test: `/availability`
- Health: `/health`
- Metrics: `/metrics`
- Docs: `/docs`

### Agent Panel URLs
- Login: `/login`
- Register: `/register`
- Dashboard: `/agent`
- Availability: `/availability`
- Agreements: `/agreements`
- Bookings: `/bookings`
- Locations: `/locations`
- Docs: `/docs`

### Source Panel URLs
- Login: `/login`
- Register: `/register`
- Dashboard: `/source?tab=dashboard`
- Agreements: `/source?tab=agreements`
- Locations: `/source?tab=locations`
- Branches: `/source?tab=branches`
- Verification: `/source?tab=verification`
- Docs: `/source?tab=docs`

---

## Support

For issues or questions:

1. **Check Documentation:** See `/docs` in each application
2. **View Logs:** Check system logs in Admin panel
3. **Contact Support:** Use Support page in each application
4. **Check Backend:** Verify backend is running and accessible

---

**Last Updated:** October 2025
**Version:** 1.0

