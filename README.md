# ☕ CAFÉ COMMAND CENTER

A complete, production-ready café management ecosystem with three interconnected modules: **Customer Ordering App**, **Kitchen Display System (KDS)**, and **Admin Dashboard**.

## 🎯 Overview

Café Command Center is a modern, real-time café management system built with cutting-edge technology to rival industry leaders like Domino's and KFC digital experiences, with a unique, Instagram-worthy aesthetic.

## ✨ Key Features

### 📱 MODULE 1: Customer Ordering Interface

**Visual Identity**: Warm, artisanal café culture meets futuristic frictionless ordering

**Features**:
- **Smart Menu Discovery**: Animated category filters (Hot Drinks, Cold Brews, Pastries, Meals) with horizontal scroll
- **Visual Customization Builder**: Step-by-step drink builder with real-time price updates
  - Size selection (Small, Medium, Large)
  - Milk type (Whole, Skim, Oat, Almond)
  - Espresso shots (+$0.75 each)
  - Syrup flavors (Vanilla, Caramel, Hazelnut)
- **Live Order Tracker**: Real-time status updates with progress animations
  - Pending → Preparing → Ready for Pickup
  - Estimated ready times
  - Live status notifications
- **Cart Management**: Drag-to-minimize cart with quantity adjustment
- **Guest Ordering**: No login required for customers

**UI Highlights**:
- Dark mode with warm amber/gold accents (#FF6B35, #F7C59F)
- Product cards with hover-to-reveal nutritional info
- Skeleton loading states and shimmer effects
- Micro-animations on every interaction

### 👨‍🍳 MODULE 2: Kitchen Display System (KDS)

**Visual Identity**: High-contrast, stress-reducing interface for high-pressure environments

**Features**:
- **Live Order Ticker**: Auto-refreshing grid with new orders and priority flags
- **Color-Coded Urgency**:
  - 🟢 Green: On time
  - 🟡 Yellow: 5+ minutes (urgent)
  - 🔴 Red: 15+ minutes (overdue with pulse animation)
- **Drag-and-Drop Kanban Board**: Move orders between status columns
  - New Orders → Preparing → Ready for Pickup
- **Real-time Synchronization**: Orders appear instantly across all devices
- **Multi-Station Support**: Separate views for Barista, Kitchen, and Packaging
- **Touch-Optimized**: Large buttons readable from 6 feet away

**UI Highlights**:
- Full-screen, distraction-free mode
- Dark theme with neon status indicators
- Touch-optimized for gloved hands
- Sound notifications for new orders (toggle-able)

### 📊 MODULE 3: Admin Dashboard & Back-Office

**Visual Identity**: Executive command center aesthetic with powerful insights

**Features**:

**Order Management**:
- Real-time sales monitor with live revenue counter
- Order history with advanced filtering
- Order status tracking
- Invoice generation

**Inventory & Supply Chain**:
- Ingredient-level stock tracking
- Auto-alerts for low stock items
- Inline editing for stock updates
- Cost analysis per item
- Supplier management

**Analytics**:
- Top selling products (bar chart)
- Order status distribution (pie chart)
- Hourly sales trends (line chart)
- Real-time revenue tracking

**Customer Insights**:
- Customer order history
- Total spending per customer
- Average order value
- Visit frequency tracking

**Staff & Operations** (Framework ready):
- Role-based access (Owner, Manager, Barista, Customer)
- Performance metrics dashboard

**UI Highlights**:
- Collapsible sidebar navigation
- Interactive charts (Recharts)
- Data tables with inline editing
- Dark/light mode compatible
- Mobile-responsive

## 🛠️ Technical Stack

### Frontend
- **Framework**: React 18.3.1 with TypeScript
- **Styling**: Tailwind CSS v4
- **Animations**: Motion (Framer Motion)
- **Routing**: React Router v7 (Data mode)
- **Charts**: Recharts
- **Drag & Drop**: react-dnd with HTML5 backend
- **UI Components**: Radix UI primitives + custom components
- **Icons**: Lucide React

### Backend
- **Runtime**: Deno (Supabase Edge Functions)
- **Web Server**: Hono.js
- **Database**: Supabase PostgreSQL
- **Real-time**: Supabase Realtime (WebSocket subscriptions)
- **Authentication**: Supabase Auth with role-based access
- **Storage**: Supabase Storage (optional, for product images)

### Infrastructure
- **Hosting**: Supabase Edge Functions
- **Database**: PostgreSQL with Row Level Security
- **Real-time Sync**: WebSocket channels
- **CORS**: Enabled for all origins (development mode)

## 📦 Database Schema

### Tables

**profiles**
- `id` (UUID, primary key)
- `email` (text, unique)
- `name` (text)
- `role` (enum: customer, barista, manager, owner)
- `created_at` (timestamp)

**categories**
- `id` (UUID, primary key)
- `name` (text)
- `slug` (text, unique)
- `icon` (text)
- `display_order` (integer)
- `created_at` (timestamp)

**products**
- `id` (UUID, primary key)
- `category_id` (UUID, foreign key → categories)
- `name` (text)
- `description` (text)
- `base_price` (decimal)
- `image_url` (text)
- `available` (boolean)
- `nutritional_info` (jsonb)
- `created_at` (timestamp)

**orders**
- `id` (UUID, primary key)
- `customer_id` (UUID, foreign key → profiles, nullable)
- `customer_name` (text)
- `status` (enum: pending, preparing, ready, completed, cancelled)
- `total_amount` (decimal)
- `payment_method` (text)
- `payment_status` (text)
- `notes` (text, nullable)
- `created_at` (timestamp)
- `updated_at` (timestamp)
- `estimated_ready_time` (timestamp)

**order_items**
- `id` (UUID, primary key)
- `order_id` (UUID, foreign key → orders)
- `product_id` (UUID, foreign key → products)
- `quantity` (integer)
- `unit_price` (decimal)
- `customizations` (jsonb)
- `created_at` (timestamp)

**inventory**
- `id` (UUID, primary key)
- `name` (text)
- `current_stock` (decimal)
- `unit` (text)
- `reorder_point` (decimal)
- `cost_per_unit` (decimal)
- `supplier` (text, nullable)
- `last_restock_date` (timestamp, nullable)
- `created_at` (timestamp)

## 🚀 Getting Started

### Prerequisites
- Supabase account connected to this project
- Node.js 18+ (for local development)

### Database Setup

**IMPORTANT**: The database tables need to be created manually via the Supabase UI or SQL editor.

Run this SQL in your Supabase SQL Editor:

\`\`\`sql
-- Profiles table
CREATE TABLE IF NOT EXISTS profiles (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT UNIQUE NOT NULL,
  name TEXT NOT NULL,
  role TEXT NOT NULL CHECK (role IN ('customer', 'barista', 'manager', 'owner')),
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Categories table
CREATE TABLE IF NOT EXISTS categories (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  icon TEXT NOT NULL,
  display_order INTEGER NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Products table
CREATE TABLE IF NOT EXISTS products (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  category_id UUID REFERENCES categories(id) ON DELETE CASCADE,
  name TEXT NOT NULL,
  description TEXT NOT NULL,
  base_price DECIMAL(10,2) NOT NULL,
  image_url TEXT,
  available BOOLEAN DEFAULT true,
  nutritional_info JSONB,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Orders table
CREATE TABLE IF NOT EXISTS orders (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  customer_id UUID REFERENCES profiles(id) ON DELETE SET NULL,
  customer_name TEXT NOT NULL,
  status TEXT NOT NULL CHECK (status IN ('pending', 'preparing', 'ready', 'completed', 'cancelled')),
  total_amount DECIMAL(10,2) NOT NULL,
  payment_method TEXT NOT NULL,
  payment_status TEXT NOT NULL,
  notes TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  estimated_ready_time TIMESTAMPTZ
);

-- Order Items table
CREATE TABLE IF NOT EXISTS order_items (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  order_id UUID REFERENCES orders(id) ON DELETE CASCADE,
  product_id UUID REFERENCES products(id),
  quantity INTEGER NOT NULL,
  unit_price DECIMAL(10,2) NOT NULL,
  customizations JSONB,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Inventory table
CREATE TABLE IF NOT EXISTS inventory (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  current_stock DECIMAL(10,2) NOT NULL,
  unit TEXT NOT NULL,
  reorder_point DECIMAL(10,2) NOT NULL,
  cost_per_unit DECIMAL(10,2) NOT NULL,
  supplier TEXT,
  last_restock_date TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Enable realtime for orders and inventory
ALTER PUBLICATION supabase_realtime ADD TABLE orders;
ALTER PUBLICATION supabase_realtime ADD TABLE order_items;
ALTER PUBLICATION supabase_realtime ADD TABLE inventory;
\`\`\`

### Seed Data

The backend will automatically seed initial data when it starts:
- 4 categories (Hot Drinks, Cold Brews, Pastries, Meals)
- 9 products across categories
- 7 inventory items

## 🎮 Usage Guide

### For Customers
1. Navigate to `/order` or click "Order" in the bottom navigation
2. Browse products by category
3. Click "Add" on any product to customize it
4. Adjust size, milk type, espresso shots, and syrups
5. Add to cart and proceed to checkout
6. Enter your name and complete the mock payment
7. Track your order in real-time until it's ready!

### For Kitchen Staff
1. Navigate to `/kitchen` or click "Kitchen" in the bottom navigation
2. View all incoming orders in the "New Orders" column
3. Drag orders to "Preparing" when you start making them
4. Drag to "Ready for Pickup" when complete
5. Color coding helps prioritize:
   - Green: On time
   - Yellow: Getting close to deadline
   - Red: Overdue (needs immediate attention)

### For Managers/Owners
1. Navigate to `/admin` or click "Admin" in the bottom navigation
2. **Overview Tab**: See today's stats and recent orders
3. **Orders Tab**: View all orders with filtering
4. **Inventory Tab**: Manage stock levels (click "Edit" to update quantities)
5. **Analytics Tab**: View sales trends and top products
6. **Customers Tab**: See customer insights and spending patterns
7. **Settings Tab**: Configure café settings

## 🔄 Real-time Features

The system uses Supabase Realtime for live updates:

1. **Order Creation**: New orders instantly appear on KDS
2. **Status Updates**: When kitchen updates order status, customer sees it immediately
3. **Inventory Alerts**: Low stock alerts trigger across all admin sessions
4. **Revenue Counter**: Live revenue updates in admin dashboard

## 🎨 Design System

### Colors
- **Primary Accent**: `#FF6B35` (Amber 500)
- **Secondary Accent**: `#F7C59F` (Amber 300)
- **Background Dark**: `#0A0A0A` (Neutral 950)
- **Card Background**: `#171717` (Neutral 900)
- **Border**: `#262626` (Neutral 800)

### Typography
- **Font Stack**: System UI fonts for optimal performance
- **Weights**: 400 (normal), 500 (medium), 700 (bold)

### Animation Principles
- **Duration**: 200-300ms for micro-interactions
- **Easing**: Spring physics for natural motion
- **Purpose**: Every animation serves UX (loading states, state changes, confirmations)

## 🔐 Security Notes

**Current Implementation (MVP)**:
- Mock payment flow (no real transactions)
- Email confirmation auto-enabled (no email server configured)
- Public anon key used for all client requests
- Service role key only used on backend

**Production Recommendations**:
1. Integrate real payment gateway (Stripe, Square)
2. Configure Supabase auth with email provider
3. Set up Row Level Security (RLS) policies
4. Implement rate limiting
5. Add audit logging
6. Enable HTTPS only
7. Configure CORS for specific origins

## 📱 Responsive Design

- **Mobile**: Optimized for ordering on phones
- **Tablet**: Kitchen display works well on tablets
- **Desktop**: Admin dashboard best on desktop/laptop
- **Touch**: All interfaces are touch-optimized

## 🧪 Testing Tips

1. **Multi-tab Testing**: Open Customer, Kitchen, and Admin in separate tabs to see real-time sync
2. **Place an Order**: Watch it appear instantly in Kitchen Display
3. **Update Status**: Move order in Kitchen, see progress in Customer tracker
4. **Low Stock**: Edit inventory below reorder point to trigger alerts

## 🚧 Future Enhancements (Phase 2+)

- [ ] Stripe payment integration
- [ ] QR code table ordering
- [ ] Push notifications for customers
- [ ] Staff scheduling calendar
- [ ] Advanced reporting (PDF exports)
- [ ] Multi-location support
- [ ] Loyalty program gamification
- [ ] Social sharing features
- [ ] AR menu preview
- [ ] Voice ordering integration

## 📄 API Endpoints

All endpoints are prefixed with `/make-server-75af47e3`:

**Categories**
- `GET /categories` - Get all categories

**Products**
- `GET /products` - Get all products
- `GET /products?category={slug}` - Get products by category

**Orders**
- `POST /orders` - Create new order
- `GET /orders` - Get all orders
- `GET /orders?status={status}` - Get orders by status
- `PUT /orders/:id/status` - Update order status

**Inventory**
- `GET /inventory` - Get all inventory items
- `PUT /inventory/:id` - Update inventory item

**Authentication**
- `POST /auth/signup` - Create new user account

## 🙏 Credits

- **UI Components**: Radix UI, Shadcn/ui
- **Icons**: Lucide React
- **Images**: Unsplash (product photos)
- **Charts**: Recharts

## 📞 Support

For issues or questions about this prototype:
- Check the browser console for detailed error logs
- Verify Supabase connection is active
- Ensure database tables are created manually
- Check network tab for API request/response details

---

**Built with ❤️ for modern café experiences**
#   c a f e _ e c o s y s t e m  
 