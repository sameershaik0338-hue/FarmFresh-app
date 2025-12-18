# FarmFresh Direct Market - Quick Reference Guide

## 🚀 Quick Start

```bash
# Install dependencies
pnpm install

# Configure environment
cp .env.example .env
# Edit .env and add your API keys

# Run development server
pnpm dev

# Build for production
pnpm build

# Run linting
pnpm run lint
```

## 📁 Key File Locations

### Farmer Logic
```
src/pages/farmer/Products.tsx     # Product management UI
src/pages/farmer/Orders.tsx       # Order management UI
src/services/farmer.service.ts    # Farmer business logic
src/db/api.ts                     # Database operations
```

### Currency System
```
src/services/currency.service.ts  # Currency formatting & calculations
src/lib/location-utils.ts         # formatCurrency(), formatPrice()
```

### Location Features
```
src/services/location.service.ts              # Location business logic
src/components/location/LocationPicker.tsx    # Map component
src/components/location/LocationPermissionDialog.tsx  # Permission UI
src/data/indian-locations.ts                  # States & districts data
src/lib/location-utils.ts                     # GPS & geocoding utilities
```

### UI Components
```
src/components/ui/                # shadcn/ui components
src/components/common/            # Shared components
src/index.css                     # Design system & animations
```

## 🔧 Service Layer Quick Reference

### FarmerService

```typescript
import { FarmerService } from '@/services';

// Validation
FarmerService.validateProduct(data)
// Returns: { valid: boolean, errors: string[] }

// Product Management
await FarmerService.createProduct(farmerId, productData)
await FarmerService.updateProduct(productId, updates)
await FarmerService.deleteProduct(productId)
await FarmerService.getFarmerProducts(farmerId)

// Order Management
const { orders, stats } = await FarmerService.getFarmerOrders(farmerId)
// stats: { total, pending, confirmed, delivered, totalRevenue }

await FarmerService.updateOrderStatus(orderId, 'confirmed')

// Inventory
FarmerService.calculateInventoryValue(products)
FarmerService.getLowStockProducts(products)
FarmerService.formatProductForDisplay(product)

// Profile
await FarmerService.updateFarmerProfile(farmerId, updates)
FarmerService.hasLocationSetup(profile)
```

### CurrencyService

```typescript
import { CurrencyService } from '@/services';

// Formatting
CurrencyService.format(1234.56)              // "₹1,234.56"
CurrencyService.formatWithUnit(40, 'kg')     // "₹40 / kg"
CurrencyService.formatIndianStyle(123456)    // "₹1,23,456"
CurrencyService.formatCompact(150000)        // "₹1.5L"

// Calculations
CurrencyService.calculateTotal(items)
CurrencyService.applyDiscount(100, 10)       // 90
CurrencyService.calculateTax(amount, 18)

// Validation
CurrencyService.validatePrice(40)
// Returns: { valid: boolean, error?: string }

// Order Summary
CurrencyService.formatOrderSummary(items, deliveryCharge)
// Returns: { subtotal, deliveryCharge, total, subtotalValue, totalValue }
```

### LocationService

```typescript
import { LocationService } from '@/services';

// Permission & Detection
await LocationService.checkLocationPermission()
// Returns: { granted, denied, prompt }

await LocationService.requestCurrentLocation()
// Returns: { latitude, longitude }

await LocationService.autoDetectAndSaveLocation(userId, apiKey)
// Detects, geocodes, and saves to database

// Distance
LocationService.calculateDistanceToFarmer(userLat, userLng, farmerLat, farmerLng)
// Returns: distance in km

LocationService.getDistanceCategory(5.5)
// Returns: { category: 'nearby', label: 'Very Close', color: 'text-green-600' }

LocationService.filterFarmersByDistance(userLat, userLng, farmers, 20)
// Returns: farmer IDs within 20km

// Validation & Formatting
LocationService.validateLocationData(data)
LocationService.formatLocationDisplay(profile)
LocationService.hasLocationSetup(profile)

// Utilities
LocationService.isLocationAvailable()
LocationService.getLocationPermissionInstructions()
```

## 💾 Database API Quick Reference

### Products API

```typescript
import { productsApi } from '@/db/api';

// Get products
await productsApi.getAllProducts()
await productsApi.getAllProducts({ category: 'vegetables' })
await productsApi.getAllProducts({ farmerId: 'uuid' })
await productsApi.getProductById(productId)
await productsApi.getProductsByFarmer(farmerId)

// Manage products
await productsApi.createProduct(farmerId, {
  name: 'Tomatoes',
  category: 'vegetables',
  price: 40,
  quantity: 100,
  unit: 'kg',
  description: 'Fresh organic tomatoes',
  images: ['url1', 'url2']
})

await productsApi.updateProduct(productId, { price: 45 })
await productsApi.deleteProduct(productId)
```

### Orders API

```typescript
import { ordersApi } from '@/db/api';

// Get orders
await ordersApi.getCustomerOrders(customerId)
await ordersApi.getFarmerOrders(farmerId)
await ordersApi.getOrderById(orderId)

// Create order
await ordersApi.createOrder(customerId, {
  farmer_id: 'uuid',
  total_amount: 500,
  delivery_address: 'Address',
  customer_phone: '1234567890',
  items: [
    { product_id: 'uuid', quantity: 2, price: 40, product_name: 'Tomatoes' }
  ]
})

// Update order
await ordersApi.updateOrderStatus(orderId, 'confirmed')
```

### Cart API

```typescript
import { cartApi } from '@/db/api';

await cartApi.getCartItems(customerId)
await cartApi.addToCart(customerId, productId, quantity)
await cartApi.updateCartItem(cartItemId, newQuantity)
await cartApi.removeFromCart(cartItemId)
await cartApi.clearCart(customerId)
```

### Profiles API

```typescript
import { profilesApi } from '@/db/api';

await profilesApi.getProfile(userId)
await profilesApi.updateProfile(userId, {
  farm_name: 'Green Farm',
  latitude: 17.385,
  longitude: 78.486,
  state: 'Telangana',
  district: 'Hyderabad'
})
await profilesApi.getAllFarmers()
```

## 🎨 UI Components Quick Reference

### Buttons

```typescript
import { Button } from '@/components/ui/button';

<Button>Default</Button>
<Button variant="outline">Outline</Button>
<Button variant="destructive">Delete</Button>
<Button className="button-3d">3D Effect</Button>
<Button size="lg" className="min-h-[44px]">Large Touch Target</Button>
```

### Cards

```typescript
import { Card, CardHeader, CardTitle, CardContent, CardFooter } from '@/components/ui/card';

<Card className="card-3d">
  <CardHeader>
    <CardTitle>Product Name</CardTitle>
  </CardHeader>
  <CardContent>
    Content here
  </CardContent>
  <CardFooter>
    Footer actions
  </CardFooter>
</Card>
```

### Badges

```typescript
import { Badge } from '@/components/ui/badge';

<Badge>Default</Badge>
<Badge variant="outline">Outline</Badge>
<Badge className="bg-green-100 text-green-800">Custom</Badge>
```

### Forms

```typescript
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select';

<div>
  <Label htmlFor="name">Product Name</Label>
  <Input id="name" value={name} onChange={(e) => setName(e.target.value)} />
</div>

<Select value={category} onValueChange={setCategory}>
  <SelectTrigger>
    <SelectValue placeholder="Select category" />
  </SelectTrigger>
  <SelectContent>
    <SelectItem value="vegetables">Vegetables</SelectItem>
    <SelectItem value="fruits">Fruits</SelectItem>
  </SelectContent>
</Select>
```

### Location Components

```typescript
import LocationPicker from '@/components/location/LocationPicker';
import LocationPermissionDialog from '@/components/location/LocationPermissionDialog';

<LocationPicker
  initialLocation={{ latitude: 17.385, longitude: 78.486 }}
  onLocationChange={(location) => console.log(location)}
/>

<LocationPermissionDialog
  open={showDialog}
  onOpenChange={setShowDialog}
  userId={userId}
  onLocationDetected={(location) => console.log(location)}
/>
```

## 🎭 Animation Classes

```css
/* Entrance animations */
.animate-scale-in          /* Scale and fade in */
.animate-bounce-slow       /* Slow bounce */

/* Hover effects */
.button-3d                 /* 3D button with hover lift */
.card-3d                   /* 3D card with hover tilt */
.image-3d                  /* Image hover transform */

/* Icon animations */
.icon-float                /* Floating animation */
.animate-float             /* Float up and down */
.animate-float-delayed     /* Float with delay */
```

## 🔐 Authentication Quick Reference

```typescript
import { useAuth } from '@/contexts/AuthContext';

const { user, profile, signIn, signUp, signOut, loading } = useAuth();

// Sign in
await signIn(email, password);

// Sign up
await signUp(email, password, username, 'farmer');

// Sign out
await signOut();

// Check role
if (profile?.role === 'farmer') {
  // Farmer-specific logic
}

// Check authentication
if (!user) {
  // Redirect to login
}
```

## 📊 Common Patterns

### Loading State

```typescript
const [loading, setLoading] = useState(false);

const handleAction = async () => {
  setLoading(true);
  try {
    await someAsyncOperation();
    toast.success('Success!');
  } catch (error) {
    toast.error('Failed');
  } finally {
    setLoading(false);
  }
};

return (
  <Button disabled={loading}>
    {loading ? <Loader2 className="animate-spin" /> : 'Submit'}
  </Button>
);
```

### Form Validation

```typescript
const [errors, setErrors] = useState<string[]>([]);

const handleSubmit = async (e: React.FormEvent) => {
  e.preventDefault();
  
  const validation = FarmerService.validateProduct(formData);
  if (!validation.valid) {
    setErrors(validation.errors);
    return;
  }
  
  // Proceed with submission
};
```

### Distance Display

```typescript
import { LocationService, CurrencyService } from '@/services';

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
      <CardHeader>
        <CardTitle>{product.name}</CardTitle>
        <p>{CurrencyService.formatWithUnit(product.price, product.unit)}</p>
      </CardHeader>
      <CardContent>
        {distanceInfo && (
          <Badge className={distanceInfo.color}>
            📍 {distance.toFixed(1)} km - {distanceInfo.label}
          </Badge>
        )}
      </CardContent>
    </Card>
  );
};
```

## 🌍 Environment Variables

```env
# Supabase
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key

# Google Maps
VITE_GOOGLE_MAPS_API_KEY=AIzaSyC...your-key

# App
VITE_APP_ID=your-app-id
VITE_API_ENV=production
```

## 🐛 Common Issues & Solutions

### Issue: Location not detecting
```typescript
// Check if location services are available
if (!LocationService.isLocationAvailable()) {
  toast.error('Location services not available');
  return;
}

// Check permission status
const permission = await LocationService.checkLocationPermission();
if (permission.denied) {
  toast.error('Location permission denied. Please enable in browser settings.');
  return;
}
```

### Issue: Google Maps not loading
```typescript
// Verify API key is set
const apiKey = import.meta.env.VITE_GOOGLE_MAPS_API_KEY;
if (!apiKey) {
  console.error('Google Maps API key not configured');
  return;
}

// Check if APIs are enabled in Google Cloud Console:
// - Maps JavaScript API
// - Geocoding API
```

### Issue: Currency not displaying correctly
```typescript
// Always use CurrencyService for formatting
import { CurrencyService } from '@/services';

// ❌ Wrong
<span>₹{price}</span>

// ✅ Correct
<span>{CurrencyService.format(price)}</span>
<span>{CurrencyService.formatWithUnit(price, unit)}</span>
```

## 📚 Documentation Files

- **ANALYSIS.md** - Complete architecture analysis
- **IMPLEMENTATION_GUIDE.md** - Detailed implementation guide
- **LOCATION_SETUP.md** - Google Maps setup instructions
- **SUMMARY.md** - Comprehensive feature summary
- **ARCHITECTURE.md** - System architecture diagrams
- **QUICK_REFERENCE.md** - This file

## 🎯 Next Steps Checklist

- [ ] Configure Google Maps API key in `.env`
- [ ] Test location detection on different browsers
- [ ] Test on mobile devices
- [ ] Verify all currency displays show ₹
- [ ] Test farmer product management flow
- [ ] Test customer ordering flow
- [ ] Check distance calculations accuracy
- [ ] Review and customize color scheme if needed
- [ ] Add custom branding/logo
- [ ] Deploy to production

---

**Quick Help:**
- Stuck? Check IMPLEMENTATION_GUIDE.md
- Need API details? Check SUMMARY.md
- Architecture questions? Check ARCHITECTURE.md
- Setup issues? Check LOCATION_SETUP.md

**Version:** 2.0.0  
**Last Updated:** 2025-12-17
