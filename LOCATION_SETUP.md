# Location Features Setup Guide

## Overview
FarmFresh Direct Market now includes comprehensive location features to connect farmers and customers based on their geographic proximity. This guide explains how to set up and use these features.

## Features Implemented

### 1. **Indian Location System**
- Default country: India
- State dropdown with 15 major Indian states
- Dynamic district loading based on selected state
- Comprehensive coverage of major agricultural regions

### 2. **GPS Location Detection**
- Browser geolocation API integration
- One-click location detection
- Automatic coordinate capture (latitude/longitude)
- Real-time location accuracy

### 3. **Google Maps Integration**
- Interactive map interface
- Pin dropping and dragging
- Reverse geocoding (coordinates → address)
- Manual location adjustment
- Visual location confirmation

### 4. **Distance Calculation**
- Haversine formula for accurate distance measurement
- Displays distance between farmers and customers
- Location-based product filtering
- Nearby farmer discovery

### 5. **Currency Update**
- All prices displayed in Indian Rupees (₹)
- Format: ₹40 / kg
- Consistent currency formatting throughout the app

## Google Maps API Setup

### Step 1: Get Your API Key

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select an existing one
3. Enable the following APIs:
   - **Maps JavaScript API**
   - **Geocoding API**
4. Go to "Credentials" → "Create Credentials" → "API Key"
5. Copy your API key

### Step 2: Secure Your API Key (Recommended)

1. Click on your API key to edit it
2. Under "Application restrictions":
   - Select "HTTP referrers (web sites)"
   - Add your domain (e.g., `yourdomain.com/*`)
   - For development, add `localhost:*`
3. Under "API restrictions":
   - Select "Restrict key"
   - Choose only the APIs you need:
     - Maps JavaScript API
     - Geocoding API
4. Save your changes

### Step 3: Configure Your Application

1. Open the `.env` file in your project root
2. Replace `YOUR_GOOGLE_MAPS_API_KEY_HERE` with your actual API key:
   ```
   VITE_GOOGLE_MAPS_API_KEY=AIzaSyC...your-actual-key-here
   ```
3. Save the file
4. Restart your development server

## How to Use Location Features

### For Customers:

1. **Set Your Location**:
   - Go to your Profile page
   - Click "Set Your Location" button
   - Choose one of two methods:
     - **GPS Detection**: Click "Use My Current Location" for automatic detection
     - **Manual Selection**: Click on the map to place a pin
   - Adjust the pin by dragging if needed
   - Select your state and district from dropdowns
   - Enter your full address
   - Click "Save Location"
   - Click "Save Changes" to update your profile

2. **Find Nearby Farmers**:
   - Once your location is set, the home page will show distance to each farmer
   - Products display "📍 X.X km away" indicator
   - Browse products from nearby farmers

### For Farmers:

1. **Set Your Farm Location**:
   - Go to your Profile page
   - Fill in farm details (name, location, description)
   - Click "Set Your Location" button
   - Use GPS or manually select your farm location on the map
   - Confirm state and district
   - Enter your complete farm address
   - Click "Save Location"
   - Click "Save Changes" to update your profile

2. **Benefits**:
   - Customers can find you based on proximity
   - Your products show distance to customers
   - Increased visibility to local customers

## Database Schema

### New Columns in `profiles` Table:
- `country` (text, default: 'India')
- `state` (text)
- `district` (text)
- `latitude` (double precision)
- `longitude` (double precision)
- `full_address` (text)

### New Functions:
- `calculate_distance(lat1, lon1, lat2, lon2)` - Returns distance in kilometers

### New Views:
- `products_with_location` - Products with farmer location data

## Supported States and Districts

The application includes comprehensive data for these Indian states:
- Andhra Pradesh
- Bihar
- Gujarat
- Haryana
- Karnataka
- Kerala
- Madhya Pradesh
- Maharashtra
- Punjab
- Rajasthan
- Tamil Nadu
- Telangana
- Uttar Pradesh
- West Bengal
- Delhi (NCT)

Each state includes all major districts for accurate location selection.

## Technical Details

### Location Utilities (`src/lib/location-utils.ts`):
- `getCurrentLocation()` - Get user's GPS coordinates
- `reverseGeocode()` - Convert coordinates to address
- `calculateDistance()` - Calculate distance between two points
- `formatDistance()` - Format distance for display
- `formatCurrency()` - Format prices in ₹ INR
- `formatPrice()` - Format price with unit

### Components:
- `LocationPicker` - Interactive map component with GPS and manual selection
- Integrated into Profile page for both farmers and customers

### API Integration:
- Google Maps JavaScript API for map display
- Google Geocoding API for address lookup
- Browser Geolocation API for GPS access

## Troubleshooting

### Location Not Detecting:
- Ensure browser location permissions are enabled
- Check if HTTPS is enabled (required for geolocation)
- Verify GPS is enabled on your device

### Map Not Loading:
- Verify your Google Maps API key is correct in `.env`
- Check that Maps JavaScript API is enabled in Google Cloud Console
- Ensure API key restrictions allow your domain
- Check browser console for error messages

### Distance Not Showing:
- Ensure both farmer and customer have set their locations
- Verify latitude and longitude are saved in the database
- Check that coordinates are valid numbers

### Geocoding Errors:
- Verify Geocoding API is enabled in Google Cloud Console
- Check API key has permission for Geocoding API
- Ensure you haven't exceeded API quota limits

## Cost Considerations

Google Maps APIs have free tier limits:
- **Maps JavaScript API**: $200 free credit per month
- **Geocoding API**: $200 free credit per month
- Typical usage for small to medium apps stays within free tier

Monitor your usage in Google Cloud Console to avoid unexpected charges.

## Privacy & Security

- Location data is stored securely in the database
- Users must explicitly grant location permissions
- GPS coordinates are only captured when user clicks the button
- Location data is used only for distance calculations and farmer discovery
- No location tracking or continuous monitoring

## Future Enhancements

Potential improvements for location features:
- Route mapping between farmer and customer
- Delivery radius settings for farmers
- Location-based notifications
- Geofencing for delivery zones
- Multi-location support for farmers with multiple farms
- Location history and analytics

## Support

For issues or questions:
1. Check this documentation first
2. Verify your Google Maps API setup
3. Check browser console for error messages
4. Ensure all environment variables are set correctly

---

**Note**: Remember to keep your Google Maps API key secure and never commit it to public repositories. Always use environment variables for sensitive configuration.
