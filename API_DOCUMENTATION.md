# Vigovia Travel Application - API Documentation

## Overview
The Vigovia Travel Application is a full-stack web application that generates professional travel itineraries in PDF format. This document provides comprehensive information about the available APIs and backend architecture.

## Application Architecture

### Technology Stack
- **Frontend**: React 19 + Vite + Tailwind CSS
- **Backend**: Node.js + Express.js
- **PDF Generation**: Puppeteer + Handlebars
- **Validation**: Joi schema validation
- **Deployment**: Docker + Docker Compose

### Project Structure
```
travel/
├── src/                     # Frontend React application
│   ├── components/          # React components
│   ├── services/           # API service layer
│   └── App.jsx             # Main app component
├── backend/                # Backend Node.js application
│   ├── src/
│   │   ├── controllers/    # Request handlers
│   │   ├── routes/         # API route definitions
│   │   ├── services/       # Business logic services
│   │   ├── validators/     # Input validation schemas
│   │   └── templates/      # PDF generation templates
│   ├── generated-pdfs/     # Generated PDF storage
│   └── server.js           # Express server entry point
└── docker-compose.yml      # Container orchestration
```

## API Endpoints

### 1. Health Check Endpoints

#### Basic Health Check
```http
GET /health
```
**Description**: Basic server health status check

**Response**:
```json
{
  "status": "OK",
  "message": "Vigovia Travel API is running",
  "timestamp": "2024-01-01T00:00:00.000Z",
  "version": "1.0.0"
}
```

#### API Health Check (Docker)
```http
GET /api/health
```
**Description**: Health check endpoint optimized for Docker containers

**Response**:
```json
{
  "status": "OK",
  "service": "vigovia-travel-api",
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

### 2. Main Itinerary Generation API

#### Generate Travel Itinerary
```http
POST /api/generate-itinerary
```
**Description**: Generates a professional travel itinerary PDF with all provided details

**Content-Type**: `application/json`

**Request Body Structure**:
```json
{
  "userDetails": {
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "+1234567890",
    "numberOfTravelers": 2
  },
  "tripDetails": {
    "destination": "Paris, France",
    "departureFrom": "New York, USA",
    "departureDate": "2024-06-15",
    "arrivalDate": "2024-06-22",
    "numberOfDays": 7,
    "numberOfNights": 6,
    "tripType": "leisure"
  },
  "activities": [
    {
      "activityId": "act_1",
      "activityName": "Eiffel Tower Visit",
      "description": "Visit the iconic Eiffel Tower",
      "day": 1,
      "timeSlot": "Morning",
      "price": 50,
      "duration": "2 hours",
      "notes": "Book tickets in advance"
    }
  ],
  "flights": [
    {
      "date": "2024-06-15",
      "airline": "Air France",
      "route": "JFK → CDG",
      "flightNumber": "AF007",
      "departureTime": "10:00 AM",
      "arrivalTime": "11:00 PM",
      "class": "Economy"
    }
  ],
  "hotels": [
    {
      "city": "Paris",
      "checkIn": "2024-06-15",
      "checkOut": "2024-06-22",
      "nights": 6,
      "hotelName": "Hotel de Paris",
      "roomType": "Deluxe",
      "address": "123 Rue de la Paix, Paris",
      "rating": 4
    }
  ],
  "additionalServices": {
    "scopeOfServices": ["Airport transfers", "City tours"],
    "specialNotes": "Vegetarian meals preferred",
    "paymentPlan": {
      "totalAmount": 2500,
      "currency": "USD",
      "installments": [
        {
          "installmentNumber": 1,
          "amount": 1000,
          "dueDate": "2024-05-15",
          "description": "Initial deposit"
        }
      ]
    },
    "visa": {
      "required": true,
      "type": "Tourist",
      "validity": "90 days",
      "processingDate": "2024-05-01"
    }
  }
}
```

**Success Response** (200):
```json
{
  "success": true,
  "message": "Itinerary generated successfully",
  "data": {
    "filename": "itinerary_John_Doe_1640995200000.pdf",
    "downloadUrl": "/generated-pdfs/itinerary_John_Doe_1640995200000.pdf",
    "generatedAt": "2024-01-01T00:00:00.000Z",
    "tripSummary": {
      "destination": "Paris, France",
      "duration": "7 days, 6 nights",
      "travelers": 2,
      "activitiesCount": 1,
      "flightsCount": 1,
      "hotelsCount": 1
    }
  }
}
```

**Error Response** (400 - Validation Error):
```json
{
  "success": false,
  "message": "Validation failed",
  "errors": [
    {
      "field": "userDetails.email",
      "message": "\"userDetails.email\" must be a valid email",
      "value": "invalid-email"
    }
  ],
  "errorCount": 1
}
```

**Error Response** (500 - Server Error):
```json
{
  "success": false,
  "message": "Failed to generate itinerary",
  "error": "PDF generation failed",
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

### 3. File Serving Endpoints

#### Download Generated PDF
```http
GET /generated-pdfs/{filename}
```
**Description**: Serves the generated PDF files for download

**Parameters**:
- `filename`: The PDF filename returned from the generate-itinerary endpoint

**Response**: Binary PDF file with appropriate headers

### 4. Test Endpoints

#### PDF Generation Test
```http
GET /api/test-pdf
```
**Description**: Test endpoint to verify PDF generation functionality

**Response**:
```json
{
  "message": "PDF generation test endpoint",
  "status": "Available"
}
```

### 5. Error Handling

#### 404 - Route Not Found
All undefined routes return:
```json
{
  "success": false,
  "message": "Route not found",
  "availableRoutes": [
    "GET /health",
    "POST /api/generate-itinerary"
  ]
}
```

## Data Validation Schema

### Validation Rules
The API uses Joi schema validation with the following rules:

#### User Details
- `name`: String, 2-100 characters, required
- `email`: Valid email address, required
- `phone`: String, 10-15 characters, required
- `numberOfTravelers`: Integer, 1-50, required

#### Trip Details
- `destination`: String, 2-100 characters, required
- `departureFrom`: String, 2-100 characters, required
- `departureDate`: ISO date string, required
- `arrivalDate`: ISO date string, required
- `numberOfDays`: Integer, 1-365, default 1
- `numberOfNights`: Integer, 0-364, default 0
- `tripType`: Enum ['exploration', 'business', 'leisure', 'adventure', 'cultural', 'romantic', 'family']

#### Activities
- `activityId`: String, required
- `activityName`: String, 2-200 characters, required
- `day`: Integer, minimum 1, required
- `timeSlot`: Enum ['Morning', 'Afternoon', 'Evening'], required
- `price`: Number, minimum 0, default 0
- Array must contain at least 1 activity

#### Flights (Optional)
- `date`: ISO date string, required
- `airline`: String, 2-100 characters, required
- `route`: String, 5-200 characters, required
- `flightNumber`: String, 2-20 characters, required
- `class`: Enum ['Economy', 'Business', 'First'], default 'Economy'

#### Hotels (Optional)
- `city`: String, 2-100 characters, required
- `checkIn`: ISO date string, required
- `checkOut`: ISO date string, required
- `nights`: Integer, minimum 1, required
- `hotelName`: String, 2-200 characters, required
- `rating`: Number, 1-5, optional

## Backend Architecture Details

### Middleware Stack
1. **Helmet**: Security headers
2. **CORS**: Cross-origin resource sharing
3. **Express JSON/URL Encoded**: Body parsing (10MB limit)
4. **Morgan**: HTTP request logging

### PDF Generation Process
1. **Template Loading**: Handlebars HTML template + compiled CSS
2. **Data Preparation**: Format dates, group activities by day, calculate totals
3. **HTML Compilation**: Merge data with template
4. **PDF Generation**: Puppeteer renders HTML to PDF
5. **File Storage**: Save to `generated-pdfs` directory
6. **Response**: Return download URL and metadata

### Security Features
- Helmet security headers
- CORS configuration with origin restrictions
- Request size limits (10MB)
- Input validation with Joi
- Non-root Docker user
- Directory creation with proper permissions

### Environment Configuration
```env
PORT=5000
NODE_ENV=development
CORS_ORIGIN=http://localhost:5173
COMPANY_NAME=Vigovia Tech Pvt. Ltd
COMPANY_ADDRESS=HD-109 Cinnabar Hills, Links Business Park, Karnataka, India
COMPANY_PHONE=+91-9999999999
COMPANY_EMAIL=contact@vigovia.com
COMPANY_WEBSITE=www.vigovia.com
PDF_OUTPUT_DIR=./generated-pdfs
TEMP_DIR=./temp
```

### Deployment
- **Frontend**: Nginx serving React build on port 3000
- **Backend**: Node.js Express server on port 5001
- **Communication**: Docker network bridge
- **Volumes**: Persistent storage for generated PDFs
- **Health Checks**: Built-in health monitoring

## Frontend Integration

### API Service Layer
The frontend uses an Axios-based API service with:
- Base URL configuration via environment variables
- Request/Response interceptors for logging
- Error handling and user-friendly error messages
- 20-second timeout for PDF generation

### Form Integration
- React Hook Form for form state management
- Real-time validation feedback
- Loading states during PDF generation
- Toast notifications for success/error states
- File download handling

## Usage Examples

### Basic Itinerary Generation
```javascript
// Frontend API call
const response = await itineraryAPI.generateItinerary({
  userDetails: { name: "John", email: "john@example.com", phone: "1234567890", numberOfTravelers: 1 },
  tripDetails: { destination: "Paris", departureFrom: "NYC", departureDate: "2024-06-15", arrivalDate: "2024-06-22" },
  activities: [{ activityId: "1", activityName: "Tour", day: 1, timeSlot: "Morning", price: 100 }],
  flights: [],
  hotels: [],
  additionalServices: {}
});

if (response.success) {
  // Download PDF using the returned URL
  window.open(response.data.data.downloadUrl, '_blank');
}
```

### Health Check
```javascript
// Check server status
const health = await itineraryAPI.healthCheck();
console.log(health.status); // "OK"
```

## Error Codes and Troubleshooting

### Common Error Scenarios
- **400**: Invalid request data (check validation errors)
- **500**: PDF generation failure (check Puppeteer/templates)
- **404**: Route not found (check endpoint URL)
- **Timeout**: Large PDFs or server overload

### Debugging Tips
- Check browser console for detailed error messages
- Verify all required fields are provided
- Ensure date formats are correct (ISO 8601)
- Check server logs for Puppeteer errors
- Verify file permissions for PDF directory

This documentation provides a comprehensive overview of the Vigovia Travel Application's API structure and backend functionality.