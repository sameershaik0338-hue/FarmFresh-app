# FarmFresh Direct Market - Implementation Guide

## 📋 Table of Contents
1. [Architecture Overview](#architecture-overview)
2. [Farmer Logic Implementation](#farmer-logic-implementation)
3. [Currency System (₹ INR)](#currency-system--inr)
4. [Location & Google Maps](#location--google-maps)
5. [UI/UX Enhancements](#uiux-enhancements)
6. [Service Layer](#service-layer)
7. [Best Practices](#best-practices)

---

## 1. Architecture Overview

### 🏗️ Project Structure

```
src/
├── components/          # Reusable UI components
│   ├── ui/             # shadcn/ui components
│   ├── location/       # Location-specific components
│   └── common/         # Shared components
├── contexts/           # React Context providers
│   └── AuthContext.tsx # Authentication & user state
├── db/                 # Database layer
│   ├── supabase.ts    # Supabase client
│   └── api.ts         # Database API functions
├── services/          # Business logic layer (NEW)
│   ├── farmer.service.ts    # Farmer operations
│   ├── location.service.ts  # Location operations
│   ├── currency.service.ts  # Currency formatting
│   └── index.ts            # Service exports
├── pages/             # Page components
│   ├── farmer/        # Farmer-specific pages
│   ├── customer/      # Customer-specific pages
│   └── *.tsx          # Shared pages
├── lib/               # Utility functions
├── data/              # Static data
├── hooks/             # Custom React hooks
└── types/             # TypeScript type definitions
```

### 🔄 Data Flow Architecture

```
UI Component (React)
    ↓
Service Layer (Business Logic)
    ↓
API Layer (db/api.ts)
    ↓
Supabase Client
    ↓
PostgreSQL Database
```

---

## 2. Farmer Logic Implementation

### 📂 File Locations

#### **Authentication**
- **File**: `src/contexts/AuthContext.tsx`
- **Purpose**: Manages authentication state for farmers and customers
- **Key Functions**:
  - `signIn(email, password)` - Login
  - `signUp(email, password, username, role)` - Registration
  - `signOut()` - Logout
  - `refreshProfile()` - Update profile data

#### **Farmer Dashboard**
- **File**: `src/pages/farmer/Products.tsx`
- **Purpose**: Product management interface
- **Features**:
  - Add new vegetables/fruits
  - Set prices in ₹ INR
  - Upload product images
  - Edit existing products
  - Delete products
  - View inventory

#### **Order Management**
- **File**: `src/pages/farmer/Orders.tsx`
- **Purpose**: Order processing interface
- **Features**:
  - View incoming orders
  - Update order status
  - View customer details
  - Track order history

### 🔧 Business Logic Separation

#### **NEW: Service Layer**

**File**: `src/services/farmer.service.ts`

```typescript
import { FarmerService } from '@/services';

// Validate product before creation
const validation = FarmerService.validateProduct(productData);
if (!validation.valid) {
  // Handle errors
}

// Create product with validation
const product = await FarmerService.createProduct(farmerId, productData);

// Get farmer's orders with statistics
const { orders, stats } = await FarmerService.getFarmerOrders(farmerId);
console.log(`Total Revenue: ${stats.totalRevenue}`);
```

**Key Methods**:
- `validateProduct()` - Validate product data
- `createProduct()` - Create with validation
- `updateProduct()` - Update with validation
- `deleteProduct()` - Soft delete
- `getFarmerProducts()` - Get all farmer's products
- `getFarmerOrders()` - Get orders with statistics
- `updateOrderStatus()` - Update order status
- `calculateInventoryValue()` - Calculate total inventory value
- `getLowStockProducts()` - Get products with low stock

### 📊 Database Operations

**File**: `src/db/api.ts`

```typescript
// Products API
export const productsApi = {
  getAllProducts: async (filters?: { category?: ProductCategory }) => {...},
  getProductById: async (id: string) => {...},
  createProduct: async (product: Product) => {...},
  updateProduct: async (id: string, updates: Partial<Product>) => {...},
  deleteProduct: async (id: string) => {...}
};

// Orders API
export const ordersApi = {
  getOrdersByFarmer: async (farmerId: string) => {...},
  updateOrderStatus: async (orderId: string, status: string) => {...}
};
```

### 🎯 Usage Example

**In Farmer Products Page**:

```typescript
import { FarmerService } from '@/services';
import { productsApi } from '@/db/api';

// Add new product
const handleAddProduct = async (formData) => {
  try {
    // Validate using service layer
    const validation = FarmerService.validateProduct(formData);
    if (!validation.valid) {
      toast.error(validation.errors.join(', '));
      return;
    }

    // Create product
    await FarmerService.createProduct(profile.id, formData);
    toast.success('Product added successfully');
    
    // Reload products
    loadProducts();
  } catch (error) {
    toast.error('Failed to add product');
  }
};
```

---

## 3. Currency System (₹ INR)

### ✅ Current Implementation

All prices are already displayed in Indian Rupees (₹) throughout the application:

- Home page: `₹{product.price}`
- Product cards: `₹{price} / {unit}`
- Cart: `₹{total.toFixed(2)}`
- Orders: `₹{order.total_amount}`

### 🆕 Enhanced Currency Service

**File**: `src/services/currency.service.ts`

```typescript
import { CurrencyService } from '@/services';

// Basic formatting
CurrencyService.format(1234.56);           // "₹1,234.56"
CurrencyService.formatWithUnit(40, 'kg');  // "₹40 / kg"

// Indian numbering system
CurrencyService.formatIndianStyle(123456); // "₹1,23,456"

// Compact format for large numbers
CurrencyService.formatCompact(150000);     // "₹1.5L"
CurrencyService.formatCompact(15000000);   // "₹1.5Cr"

// Calculations
const items = [
  { price: 40, quantity: 2 },
  { price: 60, quantity: 3 }
];
const total = CurrencyService.calculateTotal(items); // 260

// Discount calculations
const discounted = CurrencyService.applyDiscount(100, 10); // 90
const discount = CurrencyService.formatDiscount(100, 90);
// { original: "₹100", discounted: "₹90", saved: "₹10", percentage: "10%" }

// Validation
const validation = CurrencyService.validatePrice(40);
// { valid: true }

// Order summary
const summary = CurrencyService.formatOrderSummary(items, 50);
// {
//   subtotal: "₹260",
//   deliveryCharge: "₹50",
//   total: "₹310",
//   subtotalValue: 260,
//   totalValue: 310
// }
```

### 📝 Usage in Components

**Before** (Direct formatting):
```typescript
<span>${product.price}</span>
```

**After** (Using CurrencyService):
```typescript
import { CurrencyService } from '@/services';

<span>{CurrencyService.formatWithUnit(product.price, product.unit)}</span>
// Displays: "₹40 / kg"
```

### 💾 Database Storage

- Prices stored as `numeric` type in PostgreSQL
- No currency symbol in database (best practice)
- Currency formatting only at presentation layer
- Supports decimal precision for accurate calculations

---

## 4. Location & Google Maps

### 🗺️ Current Implementation

#### **Location Data**
**File**: `src/data/indian-locations.ts`
- 15 Indian states with all districts
- Telangana: 33 districts included
- Helper functions for state/district lookup

#### **Location Utilities**
**File**: `src/lib/location-utils.ts`

```typescript
import { getCurrentLocation, reverseGeocode, calculateDistance } from '@/lib/location-utils';

// Get GPS coordinates
const coords = await getCurrentLocation();
// { latitude: 17.385, longitude: 78.486 }

// Reverse geocode
const location = await reverseGeocode(17.385, 78.486, apiKey);
// { address: "...", state: "Telangana", district: "Hyderabad" }

// Calculate distance
const distance = calculateDistance(lat1, lon1, lat2, lon2);
// Returns distance in kilometers
```

#### **Location Picker Component**
**File**: `src/components/location/LocationPicker.tsx`
- Interactive Google Maps
- GPS auto-detection
- Draggable markers
- State/district dropdowns
- Reverse geocoding

### 🆕 Enhanced Location Service

**File**: `src/services/location.service.ts`

```typescript
import { LocationService } from '@/services';

// Check location permission
const permission = await LocationService.checkLocationPermission();
// { granted: true, denied: false, prompt: false }

// Auto-detect and save location
const locationData = await LocationService.autoDetectAndSaveLocation(
  userId,
  googleMapsApiKey
);
// Automatically saves to database

// Calculate distance to farmer
const distance = LocationService.calculateDistanceToFarmer(
  userLat, userLng,
  farmerLat, farmerLng
);

// Filter nearby farmers
const nearbyFarmerIds = LocationService.filterFarmersByDistance(
  userLat, userLng,
  allFarmers,
  20 // within 20 km
);

// Get distance category
const category = LocationService.getDistanceCategory(5.5);
// { category: 'nearby', label: 'Very Close', color: 'text-green-600' }

// Format location for display
const locationText = LocationService.formatLocationDisplay(profile);
// "Hyderabad, Telangana, India"

// Check if location is set up
const hasLocation = LocationService.hasLocationSetup(profile);
// true/false
```

### 🆕 Location Permission Dialog

**File**: `src/components/location/LocationPermissionDialog.tsx`

```typescript
import LocationPermissionDialog from '@/components/location/LocationPermissionDialog';

<LocationPermissionDialog
  open={showDialog}
  onOpenChange={setShowDialog}
  userId={profile.id}
  onLocationDetected={(location) => {
    console.log('Location detected:', location);
    // Update UI with new location
  }}
/>
```

**Features**:
- User-friendly permission request
- Clear benefits explanation
- Privacy information
- Error handling with instructions
- Auto-save to database

### 📍 Database Schema

**profiles table**:
```sql
ALTER TABLE profiles ADD COLUMN
  country text DEFAULT 'India',
  state text,
  district text,
  latitude double precision,
  longitude double precision,
  full_address text;
```

**Distance calculation function**:
```sql
CREATE FUNCTION calculate_distance(
  lat1 double precision,
  lon1 double precision,
  lat2 double precision,
  lon2 double precision
) RETURNS double precision;
```

### 🔧 Google Maps Setup

1. **Get API Key**:
   - Visit [Google Cloud Console](https://console.cloud.google.com/)
   - Create project
   - Enable APIs:
     - Maps JavaScript API
     - Geocoding API
   - Create credentials

2. **Configure Environment**:
   ```env
   VITE_GOOGLE_MAPS_API_KEY=AIzaSyC...your-key-here
   ```

3. **Secure API Key**:
   - Add HTTP referrer restrictions
   - Restrict to specific APIs
   - Monitor usage in console

### 🎯 Usage Examples

**Auto-detect location on login**:
```typescript
import { LocationService } from '@/services';

useEffect(() => {
  const detectLocation = async () => {
    if (!profile || LocationService.hasLocationSetup(profile)) {
      return; // Already has location
    }

    try {
      const apiKey = import.meta.env.VITE_GOOGLE_MAPS_API_KEY;
      await LocationService.autoDetectAndSaveLocation(profile.id, apiKey);
      toast.success('Location detected!');
    } catch (error) {
      console.error('Location detection failed:', error);
    }
  };

  detectLocation();
}, [profile]);
```

**Show distance on product cards**:
```typescript
import { LocationService } from '@/services';

const ProductCard = ({ product, userProfile }) => {
  const distance = userProfile.latitude && product.farmer.latitude
    ? LocationService.calculateDistanceToFarmer(
        userProfile.latitude,
        userProfile.longitude,
        product.farmer.latitude,
        product.farmer.longitude
      )
    : null;

  const distanceInfo = distance 
    ? LocationService.getDistanceCategory(distance)
    : null;

  return (
    <Card>
      {/* Product details */}
      {distanceInfo && (
        <Badge className={distanceInfo.color}>
          📍 {distance.toFixed(1)} km - {distanceInfo.label}
        </Badge>
      )}
    </Card>
  );
};
```

---

## 5. UI/UX Enhancements

### 🎨 Design System

#### **Color Scheme**
**File**: `src/index.css`

```css
:root {
  /* Primary - Agricultural Green */
  --primary: 142 76% 36%;        /* #4CAF50 */
  --primary-foreground: 0 0% 100%;
  
  /* Secondary - Warm Orange */
  --secondary: 33 100% 50%;      /* #FF9800 */
  --secondary-foreground: 0 0% 100%;
  
  /* Accent - Earth Tones */
  --accent: 45 93% 47%;
  --accent-foreground: 0 0% 100%;
}
```

#### **Animations**
**File**: `src/index.css`

```css
/* Scale-in animation */
@keyframes scale-in {
  from {
    opacity: 0;
    transform: scale(0.95);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.animate-scale-in {
  animation: scale-in 0.3s ease-out;
}

/* 3D button effect */
.button-3d {
  transform: translateY(0);
  transition: all 0.2s ease;
}

.button-3d:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
}

.button-3d:active {
  transform: translateY(0);
}

/* Icon float animation */
@keyframes icon-float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

.icon-float {
  animation: icon-float 3s ease-in-out infinite;
}
```

### 📱 Farmer-Friendly UI Features

#### **Large Touch Targets**
```typescript
// Minimum 44px height for all interactive elements
<Button className="min-h-[44px] text-lg px-6">
  Add Product
</Button>
```

#### **Clear Visual Hierarchy**
```typescript
// Large headings, clear sections
<h1 className="text-3xl font-bold mb-2">My Products</h1>
<p className="text-muted-foreground text-lg mb-6">
  Manage your vegetables and fruits
</p>
```

#### **Icon-First Design**
```typescript
// Icons with labels for clarity
<Button>
  <Plus className="w-5 h-5 mr-2" />
  Add New Product
</Button>
```

#### **Status Badges**
```typescript
// Color-coded status indicators
const statusColors = {
  pending: 'bg-yellow-100 text-yellow-800',
  confirmed: 'bg-blue-100 text-blue-800',
  delivered: 'bg-green-100 text-green-800',
  cancelled: 'bg-red-100 text-red-800'
};

<Badge className={statusColors[order.status]}>
  {order.status}
</Badge>
```

### 🎭 Animation Guidelines

1. **Entrance Animations**: Use `animate-scale-in` for cards and modals
2. **Hover Effects**: Apply `button-3d` for interactive elements
3. **Loading States**: Use `animate-spin` for loaders
4. **Icon Animations**: Apply `icon-float` for decorative icons

### 📐 Responsive Design

```typescript
// Mobile-first approach
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
  {/* Product cards */}
</div>

// Touch-friendly spacing
<div className="space-y-4 p-4">
  {/* Content */}
</div>
```

---

## 6. Service Layer

### 🏗️ Architecture Benefits

1. **Separation of Concerns**: UI logic separate from business logic
2. **Reusability**: Services can be used across multiple components
3. **Testability**: Easy to unit test business logic
4. **Maintainability**: Centralized business rules
5. **Type Safety**: Full TypeScript support

### 📦 Available Services

#### **FarmerService**
- Product validation and management
- Order processing
- Inventory calculations
- Profile management

#### **LocationService**
- GPS detection
- Permission handling
- Distance calculations
- Location validation

#### **CurrencyService**
- Price formatting
- Currency calculations
- Discount handling
- Tax calculations

### 🎯 Usage Pattern

```typescript
// Import services
import { FarmerService, LocationService, CurrencyService } from '@/services';

// Use in components
const MyComponent = () => {
  const handleAction = async () => {
    // Validate
    const validation = FarmerService.validateProduct(data);
    if (!validation.valid) {
      return;
    }

    // Process
    const result = await FarmerService.createProduct(farmerId, data);

    // Format for display
    const priceDisplay = CurrencyService.formatWithUnit(
      result.price,
      result.unit
    );
  };
};
```

---

## 7. Best Practices

### ✅ Code Quality

1. **Type Safety**: Use TypeScript interfaces for all data
2. **Error Handling**: Always wrap async operations in try-catch
3. **Validation**: Validate user input before processing
4. **Loading States**: Show loading indicators for async operations
5. **User Feedback**: Use toast notifications for actions

### 🔒 Security

1. **Environment Variables**: Store API keys in `.env`
2. **Input Validation**: Validate all user inputs
3. **Row Level Security**: Use Supabase RLS policies
4. **Permission Checks**: Verify user roles before actions

### 🚀 Performance

1. **Lazy Loading**: Load images lazily
2. **Pagination**: Implement for large lists
3. **Caching**: Cache location data in localStorage
4. **Optimistic Updates**: Update UI before server response
5. **Debouncing**: Debounce search inputs

### 📱 Accessibility

1. **Keyboard Navigation**: Support Tab, Enter, Escape
2. **Screen Readers**: Use semantic HTML and ARIA labels
3. **Color Contrast**: Ensure WCAG AA compliance
4. **Touch Targets**: Minimum 44x44px for mobile
5. **Error Messages**: Clear, actionable error messages

### 🧪 Testing

1. **Unit Tests**: Test service layer functions
2. **Integration Tests**: Test API interactions
3. **E2E Tests**: Test critical user flows
4. **Manual Testing**: Test on real devices

---

## 📚 Additional Resources

- [Supabase Documentation](https://supabase.com/docs)
- [Google Maps API](https://developers.google.com/maps)
- [Tailwind CSS](https://tailwindcss.com/docs)
- [shadcn/ui](https://ui.shadcn.com/)
- [React TypeScript](https://react-typescript-cheatsheet.netlify.app/)

---

**Last Updated**: 2025-12-17  
**Version**: 2.0.0  
**Application**: FarmFresh Direct Market
