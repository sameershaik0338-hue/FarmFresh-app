# FarmFresh Direct Market - Project Summary

## Overview
FarmFresh Direct Market is a comprehensive web platform that connects farmers directly with customers, enabling the sale of fresh vegetables and fruits without middlemen. The platform ensures fair pricing for farmers and affordable, fresh produce for customers while supporting local farming communities.

## Key Features Implemented

### 1. User Authentication & Role Management
- **Secure Authentication**: Username and password-based authentication using Supabase Auth
- **Dual Role System**: 
  - **Farmers**: Can list products, manage inventory, and process orders
  - **Customers**: Can browse products, add to cart, and place orders
- **Profile Management**: Users can update their profile information including farm details for farmers

### 2. Farmer Features
- **Product Management Dashboard**:
  - Add new products with images, descriptions, pricing, and quantity
  - Edit existing product listings
  - Delete products
  - Automatic image compression (max 1MB) with WEBP conversion
  - Support for multiple product images
  - Category selection (Vegetables/Fruits)
  
- **Order Management**:
  - View all incoming orders
  - Update order status through multiple stages:
    - Pending → Confirmed → Preparing → Ready for Pickup → Out for Delivery → Delivered
  - View customer details and delivery addresses
  - Track order history

### 3. Customer Features
- **Product Browsing**:
  - Browse all available products on the home page
  - Search products by name
  - Filter by category (Vegetables/Fruits)
  - View product details including farmer information
  
- **Shopping Cart**:
  - Add products to cart
  - Update quantities
  - Remove items
  - View cart total
  
- **Checkout & Orders**:
  - Enter delivery address and contact information
  - Add order notes
  - Place orders
  - Track order status in real-time
  - View order history

### 4. Design & User Experience
- **Fresh Green Theme**: Primary color #4CAF50 representing agriculture and freshness
- **Responsive Design**: Fully responsive layout for mobile and desktop devices
- **Modern UI**: Built with shadcn/ui components and Tailwind CSS
- **Smooth Animations**: Transitions and hover effects for better user experience
- **Dark Mode Support**: Complete dark mode implementation

## Technical Architecture

### Frontend Stack
- **React 18** with TypeScript
- **React Router** for navigation
- **Tailwind CSS** for styling
- **shadcn/ui** for UI components
- **Lucide React** for icons
- **Sonner** for toast notifications
- **date-fns** for date formatting

### Backend & Database
- **Supabase** for:
  - PostgreSQL database
  - Authentication
  - Storage (product images)
  - Row Level Security (RLS) policies
  
### Database Schema
- **profiles**: User information with role-based data
- **products**: Product listings with pricing and inventory
- **cart_items**: Shopping cart management
- **orders**: Order tracking and management
- **order_items**: Individual items within orders

### Security Features
- Row Level Security (RLS) enabled on all tables
- Role-based access control
- Secure image upload with validation
- Protected API endpoints
- Automatic user profile creation on signup

## User Flows

### Farmer Flow
1. Sign up as a farmer with farm details
2. Add products with images and pricing
3. Receive orders from customers
4. Update order status as they progress
5. Manage product inventory

### Customer Flow
1. Sign up as a customer
2. Browse available products
3. Add products to cart
4. Proceed to checkout
5. Enter delivery details
6. Place order
7. Track order status

## Image Management
- **Automatic Compression**: Images are automatically compressed to under 1MB
- **Format Conversion**: Images converted to WEBP for optimal performance
- **Resolution Limiting**: Maximum resolution of 1920px (1080p)
- **Validation**: Filename validation (English letters and numbers only)
- **Supported Formats**: JPEG, PNG, GIF, WEBP, AVIF

## Authentication Details
- **Username-based**: Uses username@miaoda.com format internally
- **No Email Verification**: Disabled for easier onboarding
- **First User**: Automatically assigned farmer role
- **Subsequent Users**: Default to customer role, can be changed during signup

## API Structure
All database operations are centralized in `/src/db/api.ts`:
- `profilesApi`: User profile management
- `productsApi`: Product CRUD operations
- `cartApi`: Shopping cart management
- `ordersApi`: Order creation and tracking
- `storageApi`: Image upload and deletion

## Color Scheme
- **Primary**: Fresh Green (HSL: 122 39% 49%)
- **Secondary**: Warm Orange (HSL: 36 100% 50%)
- **Background**: White/Dark Gray
- **Accent**: Light Green tints
- **Semantic Colors**: Success, Warning, Error states

## Responsive Breakpoints
- **Mobile**: < 768px
- **Tablet**: 768px - 1024px
- **Desktop**: > 1024px
- **Large Desktop**: > 1280px

## Future Enhancement Opportunities
- GPS-based location detection for farms
- Google Maps integration for farm locations
- Multi-language support (i18n implementation)
- Real-time notifications
- Payment gateway integration
- Rating and review system
- Advanced search with location-based filtering
- Farmer analytics dashboard
- Customer favorites/wishlist

## Important Notes
- **First User**: The first user to sign up will automatically be assigned the farmer role
- **Image Upload**: All images are automatically compressed and converted to WEBP format
- **Order Management**: Orders are grouped by farmer, so customers can order from multiple farmers in one checkout
- **Profile Updates**: Users can update their profile information at any time through the Profile page

## Getting Started
1. Sign up as a farmer or customer
2. Farmers: Add your first product
3. Customers: Browse and add products to cart
4. Complete checkout and track your orders

The platform is now fully functional and ready for use!
