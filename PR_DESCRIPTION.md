# Fix Course Registry Resource Leak and Implement list_courses Function

## Summary
This PR addresses the audit issue #30: "Check for Resource Leak in Course_Registry Map" by implementing a comprehensive course registry system with proper resource management to prevent gas limit issues.

## Problem Addressed
The audit identified that the `list_courses` function could exceed Soroban gas limits if the course registry map grows too large without proper size constraints and pagination mechanisms.

## Solution Implemented

### 1. Course Registry Infrastructure
- **Added Course Registry Map**: Implemented `CourseRegistry` and `CourseInfo` data structures
- **Size Limitation**: Set `MAX_COURSE_REGISTRY_SIZE = 1000` to prevent unbounded growth
- **TTL Management**: Added proper Time-To-Live management with `LEDGER_BUMP_THRESHOLD` and `LEDGER_BUMP_EXTEND`

### 2. Core Functions
- **`add_course_to_registry()`**: Adds courses with duplicate checking and size limits
- **`list_courses()`**: Returns all active courses with TTL extension
- **`list_courses_paginated()`**: Provides paginated access with 100-course limit per page
- **`get_course_info()`**: Retrieves detailed course information
- **`deactivate_course()`**: Admin function to mark courses as inactive
- **`cleanup_inactive_courses()`**: Removes inactive courses to free storage

### 3. Resource Leak Prevention
- **Registry Size Limits**: Prevents adding courses beyond 1000 limit
- **Pagination Limits**: Maximum 100 courses per page to control gas usage
- **Automatic TTL Extension**: Prevents data expiration during access
- **Cleanup Mechanisms**: Regular removal of inactive courses

### 4. Gas Optimization Features
- **Pagination**: Breaks large datasets into manageable chunks
- **TTL Extension**: Prevents unnecessary data recreation
- **Size Validation**: Early rejection of oversized operations
- **Efficient Storage**: Uses persistent storage with proper cleanup

## Files Modified
- `contracts/scholar_contracts/src/lib.rs`: Added course registry implementation
- `contracts/scholar_contracts/src/test.rs`: Added comprehensive test suite

## Tests Added
- **Basic Functionality**: Course addition, listing, and info retrieval
- **Size Limits**: Registry size enforcement (1000 course limit)
- **Pagination**: Paginated access with 100-course per page limit
- **Duplicate Prevention**: Prevents adding duplicate courses
- **TTL Management**: Verifies proper TTL extension
- **Gas Efficiency**: Tests with larger datasets (50+ courses)
- **Cleanup Functionality**: Inactive course removal

## Security Improvements
- **Access Control**: Admin-only functions for course management
- **Input Validation**: Proper parameter checking and limits
- **Resource Management**: Prevents storage exhaustion attacks
- **Error Handling**: Comprehensive error codes for edge cases

## Gas Impact
- **Reduced Gas Usage**: Pagination prevents large single operations
- **Predictable Costs**: Size limits provide predictable gas consumption
- **Optimized Storage**: Regular cleanup reduces storage overhead

## Backward Compatibility
- **No Breaking Changes**: All existing functionality preserved
- **Optional Features**: New course registry is additive functionality
- **Gradual Migration**: Existing courses can be gradually migrated

## Testing
All tests pass and verify:
- ✅ Course registry basic operations
- ✅ Size limit enforcement
- ✅ Pagination functionality
- ✅ TTL management
- ✅ Gas efficiency with large datasets
- ✅ Cleanup mechanisms
- ✅ Access control and security

## Pipeline Status
This PR fixes compilation issues and should pass all existing pipeline checks while adding new functionality.

## Audit Compliance
This implementation fully addresses audit issue #30 by:
- Preventing unbounded map growth
- Implementing gas-efficient pagination
- Adding proper resource management
- Providing cleanup mechanisms
- Ensuring predictable gas consumption

Closes #30
