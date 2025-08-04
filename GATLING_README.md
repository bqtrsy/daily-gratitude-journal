# Daily Gratitude Journal - Gatling Performance Test

## Overview

This comprehensive Gatling simulation tests all available API endpoints in the Daily Gratitude Journal application with proper authentication, error handling, and API coverage tracking.

## Features

✅ **Complete Authentication Flow**
- POST to `/api/authenticate` with admin credentials
- JWT token extraction and validation
- Bearer token usage for all authenticated requests

✅ **Full CRUD Operations**
- Create gratitude entries with unique UUID-based content
- Read entries by ID, date, and date range
- Update and partial update operations
- Delete operations with proper cleanup

✅ **API Coverage Tracking**
- Real-time tracking of tested vs untested endpoints
- Success/failure rate monitoring
- Comprehensive final summary report

✅ **Error Handling & Validation**
- Proper status code validation (200, 201, 204, 401, 404)
- Sequential dependency management with `.exitHereIfFailed()`
- Realistic data generation with UUIDs and timestamps

✅ **Load Testing**
- Configurable user ramp-up (10 users over 10 seconds by default)
- Realistic think-time between requests
- Concurrent user simulation

## API Endpoints Tested

### Authentication
- `POST /api/authenticate` - User authentication

### Account Management
- `GET /api/account` - Get current user account info

### Gratitude Entries (CRUD)
- `POST /api/gratitude-entries` - Create new entry
- `GET /api/gratitude-entries` - Get all entries (paginated)
- `GET /api/gratitude-entries/{id}` - Get entry by ID
- `PUT /api/gratitude-entries/{id}` - Update entry
- `PATCH /api/gratitude-entries/{id}` - Partial update entry
- `DELETE /api/gratitude-entries/{id}` - Delete entry

### Date-based Queries
- `GET /api/gratitude-entries/by-date/{date}` - Get entry by specific date
- `GET /api/gratitude-entries/today` - Get today's entry
- `GET /api/gratitude-entries/by-date-range` - Get entries within date range

### Public Endpoints
- `GET /api/users` - Get public user information

### Security Validation
- Unauthenticated access testing (expected 401 responses)

## How to Run

### Prerequisites
1. Ensure the Daily Gratitude Journal application is running on `http://localhost:8080`
2. Verify admin user exists with credentials: `admin/admin`
3. Have Gatling installed and configured

### Running the Test

```bash
# Run with default settings (10 users, 10 seconds)
./gradlew gatlingRun

# Run with custom base URL
./gradlew gatlingRun -DbaseURL=http://your-server:8080

# Run with custom user count and duration
./gradlew gatlingRun -Dusers=50 -Dramp=30
```

### Environment Variables
- `baseURL`: Application base URL (default: `http://localhost:8080`)
- `users`: Number of concurrent users (default: 10)
- `ramp`: Ramp-up duration in seconds (default: 10)

## Test Flow

1. **Authentication Phase**
   - Authenticate with admin credentials
   - Extract and validate JWT token
   - Verify authentication success

2. **Account Validation**
   - Test authenticated account endpoint access
   - Verify user session validity

3. **CRUD Operations**
   - Create gratitude entry with unique content
   - Retrieve all entries
   - Get specific entry by ID
   - Update entry content
   - Partial update (mood change)
   - Delete entry

4. **Date-based Queries**
   - Query by specific date
   - Get today's entry
   - Query by date range

5. **Public Endpoints**
   - Test public user endpoint access

6. **Security Validation**
   - Test unauthenticated access (expected 401)

7. **Summary Report**
   - API coverage statistics
   - Success/failure rates
   - Tested vs untested endpoints

## Expected Results

### Success Criteria
- ✅ 100% authentication success
- ✅ All CRUD operations complete successfully
- ✅ No 400, 401, or 404 errors (except expected 401 for unauthenticated access)
- ✅ Proper JWT token handling
- ✅ Realistic data generation and validation

### Sample Output
```
✅ Authentication successful. JWT Token: eyJhbGciOiJIUzI1NiJ9...
✅ Account info retrieved successfully
✅ Created gratitude entry with ID: 123, URL: /api/gratitude-entries/123
✅ Retrieved all gratitude entries
✅ Retrieved gratitude entry by ID: 123
✅ Retrieved gratitude entry by date: 2024-01-15
✅ Retrieved today's gratitude entry
✅ Retrieved gratitude entries by date range: 2024-01-08 to 2024-01-15
✅ Updated gratitude entry with ID: 123
✅ Partially updated gratitude entry with ID: 123
✅ Deleted gratitude entry with ID: 123
✅ Retrieved public users
✅ Unauthenticated access correctly rejected with 401

================================================================================
🎯 GATLING SIMULATION COMPLETED SUCCESSFULLY
================================================================================
📊 API Coverage: 12 / 12 (100.00%)
📈 Total Requests: 12
✅ Successful Requests: 12
❌ Failed Requests: 0

📋 TESTED ENDPOINTS:
  ✅ /api/account [GET]
  ✅ /api/authenticate [POST]
  ✅ /api/gratitude-entries [GET]
  ✅ /api/gratitude-entries [POST]
  ✅ /api/gratitude-entries/by-date-range [GET]
  ✅ /api/gratitude-entries/by-date/{date} [GET]
  ✅ /api/gratitude-entries/today [GET]
  ✅ /api/gratitude-entries/{id} [DELETE]
  ✅ /api/gratitude-entries/{id} [GET]
  ✅ /api/gratitude-entries/{id} [PATCH]
  ✅ /api/gratitude-entries/{id} [PUT]
  ✅ /api/users [GET]

📊 COVERAGE SUMMARY:
  • Authentication: ✅
  • CRUD Operations: ✅
  • Date-based Queries: ✅
  • Public Endpoints: ✅
  • Error Handling: ✅
  • Security Validation: ✅
================================================================================
```

## Data Generation

### Gratitude Entry Content
- **Entry Text**: `"Gratitude entry " + UUID.randomUUID().substring(0, 8)`
- **Date**: Current UTC date
- **Timestamp**: Current UTC timestamp
- **Mood**: Random selection from `["HAPPY", "GRATEFUL", "CONTENT", "HOPEFUL", "REFLECTIVE", "OTHER"]`

### Unique Identifiers
- UUID-based entry content ensures no conflicts
- Current timestamp prevents duplicate date issues
- Random mood selection for realistic testing

## Error Handling

### Expected Status Codes
- `200` - Successful GET, PUT, PATCH operations
- `201` - Successful POST operations
- `204` - Successful DELETE operations
- `401` - Unauthenticated access (expected for security tests)
- `404` - Not found (acceptable for date-based queries when no entry exists)

### Failure Prevention
- `.exitHereIfFailed()` ensures sequential dependency
- Proper JWT token validation before authenticated requests
- Realistic data generation to prevent validation errors
- Appropriate think-time to prevent race conditions

## Performance Metrics

The simulation tracks:
- **Total Requests**: Number of API calls attempted
- **Successful Requests**: Number of successful API calls
- **API Coverage**: Percentage of available endpoints tested
- **Response Times**: Individual request performance
- **Error Rates**: Failed request tracking

## Customization

### Adding New Endpoints
1. Create a new `ChainBuilder` method
2. Add API coverage tracking
3. Include in the main scenario chain
4. Update the summary reporting

### Modifying Load Patterns
```java
// Change user count and duration
users.injectOpen(rampUsers(50).during(Duration.ofSeconds(30)))

// Add different injection patterns
users.injectOpen(
    rampUsers(10).during(Duration.ofSeconds(10)),
    constantUsersPerSec(5).during(Duration.ofSeconds(20))
)
```

### Custom Data Generation
```java
// Custom entry content
String customEntry = "Custom gratitude: " + System.currentTimeMillis();

// Custom date ranges
String startDate = LocalDate.now().minusDays(30).toString();
String endDate = LocalDate.now().toString();
```

## Troubleshooting

### Common Issues
1. **Authentication Failures**: Verify admin user exists and credentials are correct
2. **Connection Errors**: Ensure application is running on correct port
3. **Validation Errors**: Check that generated data meets entity constraints
4. **Timeout Issues**: Increase think-time or reduce concurrent users

### Debug Mode
Enable detailed logging by modifying the HTTP configuration:
```java
.silentResources(false) // Show all request/response details
```

## Maintenance

### Regular Updates
- Update endpoint URLs if API changes
- Modify data generation for new entity fields
- Adjust load patterns based on performance requirements
- Update expected status codes for new error scenarios

### Version Compatibility
- Test with different application versions
- Verify compatibility with new API features
- Update authentication mechanisms if changed 