# Pharmacy Locator

## Overview

A map-centric web application that helps users find nearby pharmacies, grocery stores, and senior homes/nursing homes based on their location. The application provides an interactive Google Maps interface where users can search for addresses, adjust search radius, and view detailed business information including ratings, distances, directions, pharmacy rankings, and prescription counts.

**Core Purpose**: Enable users to quickly locate pharmacies, groceries, and senior homes within a customizable radius of any address, with an emphasis on visual map interaction, branded pharmacy icons, intelligent clinic filtering, adjacent clinic detection, pharmacy rankings with provincial prescription counts, and clear presentation of business counts and details.

**Tech Stack**:
- Frontend: React + TypeScript with Vite
- UI Framework: shadcn/ui components (Radix UI primitives)
- Styling: Tailwind CSS
- Maps: Google Maps JavaScript API via @react-google-maps/api
- Backend: Express.js
- Database: PostgreSQL with Drizzle ORM
- State Management: TanStack Query (React Query)
- Routing: Wouter

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture

**Component Structure**: The application follows a component-based architecture with clear separation between UI primitives and business logic components.

- **Page Components**: Single-page application centered around `PharmacyLocator` as the main interface
- **Feature Components**: Self-contained components for specific functionality:
  - `SearchBar`: Address input with loading states
  - `RadiusControl`: Dynamic radius adjustment (500m-5km) with slider and preset buttons
  - `PharmacyInfoCard`: Detailed pharmacy information with directions, remove button, rank badge, prescription counts, and adjacent clinic indicator
  - `SeniorHomeInfoCard`: Detailed senior home information with directions and remove button
  - `LocationDisplay`: Shows Province, City, and Town from geocoding results
  - `CounterDisplay`: Reusable counter component for pharmacies, groceries, and senior homes
- **UI Components**: Comprehensive shadcn/ui component library in `client/src/components/ui/` providing consistent design system
- **Layout Pattern**: Map-first design with dynamic header containing integrated controls (search, radius, counters, maximize toggle) and overlay info cards
- **Utilities**: 
  - `client/src/utils/pharmacyIcons.ts` - Custom pharmacy icon system with brand matching and fallback
  - `client/src/utils/parseRankings.ts` - Excel file parser for loading pharmacy rankings with improved address matching algorithm
  - `client/src/utils/prescriptionCounts.ts` - IQVIA provincial prescription count lookup by rank (A-E) and province

**State Management Strategy**: Combination of local React state for UI interactions and TanStack Query for data fetching/caching. The main page manages:
- Map state (center, zoom, zoom level tracking)
- Search parameters (address, radius, location info, searched location coordinates)
- Results (pharmacies, groceries, senior homes)
  - `rawGroceries`: Unfiltered grocery results from Google Maps
  - `groceries`: Deduplicated groceries (after merging with co-located pharmacies)
  - `allPharmacies`: All pharmacy results (with grocery names when merged)
- Hidden items (Sets of place IDs for pharmacies and senior homes removed from view)
- Pharmacy rankings (Map of normalized addresses to rank letters A-E)
- UI state (selected pharmacy, selected senior home, loading states, map maximized state)
- Dynamic header height using ResizeObserver for responsive layout
- **Map Maximize Feature**: Toggle button (Maximize2/Minimize2 icon) to hide/show header controls for full-screen map viewing

**Design System**: Custom Tailwind configuration extending shadcn/ui "new-york" style with:
- Custom color system using CSS variables for theme consistency
- Typography scale based on Inter font family
- Spacing primitives: 2, 4, 6, 8, 12, 16 units
- Design references: Google Maps patterns for map controls, Material Design principles for components

### Backend Architecture

**Server Framework**: Express.js with TypeScript, configured for both development (Vite integration) and production builds.

**API Structure**: 
- Routes registered through `registerRoutes()` function in `server/routes.ts`
- API endpoints prefixed with `/api`
- Request/response logging middleware for API routes
- JSON body parsing with raw body preservation

**Storage Layer**: Abstracted through `IStorage` interface allowing multiple implementations:
- Current: In-memory storage (`MemStorage`) for development
- Designed for: PostgreSQL database integration via Drizzle ORM
- CRUD operations encapsulated in storage interface methods

**Authentication Pattern**: User-based system with username/password schema, prepared for session management (express-session with connect-pg-simple)

### Data Storage

**Database**: PostgreSQL (via Neon serverless driver) configured but not yet fully implemented.

**ORM**: Drizzle ORM with:
- Schema definition in `shared/schema.ts`
- Type-safe queries and migrations
- Zod schema validation integration
- Migration output directory: `./migrations`

**Current Schema**:
- `users` table: id (UUID), username (unique), password
- Prepared for expansion with pharmacy data models

### External Dependencies

**Google Maps Platform**:
- **Service**: Google Maps JavaScript API
- **Libraries**: Places API (legacy), Geometry API
- **Usage**: 
  - Map rendering with custom styling (POI suppression)
  - Geocoding addresses to coordinates and location details (province, city, town)
  - Nearby pharmacy search (type: 'pharmacy') with clinic filtering
  - Nearby grocery search (type: 'grocery_or_supermarket')
  - Nearby senior home search (type: 'nursing_home')
  - Adjacent clinic detection (50m radius around pharmacies)
  - Distance calculations using spherical geometry
  - Directions links to Google Maps
- **Configuration**: API key stored in `VITE_GOOGLE_MAPS_API_KEY` environment variable
- **Integration**: Via `@react-google-maps/api` React wrapper library
- **Required APIs**: The following APIs must be enabled in Google Cloud Console:
  - Maps JavaScript API
  - Places API (legacy version, NOT "Places API (New)")
  - Geocoding API
- **Important**: New Google Cloud projects must manually enable the Places API in the API Library
- **Map Styling**: All default POIs hidden to show only searched pharmacy, grocery, and senior home markers
- **Clinic Filtering**: Clinics are automatically filtered out from pharmacy search results (filters by name containing "clinic", "medical centre", "medical center")
- **Adjacent Clinic Detection**: When clicking a pharmacy marker, the app checks for clinics within 50 meters and displays "Adjacent clinic" badge if found
- **Smart Grocery/Pharmacy Merging**:
  - Automatically detects when a grocery store and pharmacy are at the same location (within 50 meters)
  - **Merges them into ONE marker** showing the grocery name with full pharmacy details
  - Example: Google returns "Loblaw" (pharmacy) + "Zehrs" (grocery) → shows as "Zehrs" with Rank E and 17.3K Rx
  - Uses customer-facing store name (Zehrs, Walmart, Sobeys) instead of parent company name (Loblaw)
  - Preserves all pharmacy functionality: rank badge, prescription counts, pharmacy info card
  - Prevents duplicate markers and confusion for stores with both grocery and pharmacy services
  - Merging happens automatically in useEffect after both search results complete
- **Hide/Unhide Functionality**: 
  - Users can hide individual pharmacies and senior homes via info card's remove button (eye-off icon)
  - Hidden items tracked in separate Sets for pharmacies and senior homes
  - Excluded from map markers and counters
  - Unified "Show N Hidden" button unhides all items
  - Hidden state resets on "New Search"
- **Dynamic Logo Scaling**: Pharmacy logos and senior home icons scale based on zoom level - staying at original size at default zoom (11), scaling down when zooming out (min 0.3x scale), and never scaling up when zooming in
- **Distance Scale**: Google Maps scale control displays current map scale in meters/kilometers
- **Smart Grocery Icon System**:
  - Groceries with matching pharmacy brands (Walmart, Sobeys, Metro, etc.) show brand logos instead of shopping cart
  - Loblaw Companies Limited brands always display brand logos (SDM, Loblaws, No Frills, Real Canadian Superstore, Zehrs, Fortinos, Valu-mart, Provigo, Your Independent Grocer, Maxi, Extra Foods)
  - Non-branded groceries display black shopping cart icon
  - Prescription counts excluded from all Loblaw Companies Limited brands
- **Pharmacy Rankings with Prescription Counts**: 
  - Loaded from Excel file (`attached_assets/PHA Rank Details_1762373184125.xlsx`) containing 11,864 pharmacy records
  - Excel format: Column C contains "pharmacy name: address", Column D contains rank letters (A-E)
  - **Improved Address Matching Algorithm**:
    - Normalizes both Google Maps addresses and Excel addresses
    - Converts street types: Avenue→Ave, Street→St, Road→Rd, Drive→Dr, Court→Ct, Boulevard→Blvd
    - Removes directional indicators: East, West, North, South
    - Strips unit/apartment numbers (Unit 5, #202, Apt A, etc.)
    - Matches street numbers first, then checks word overlap (minimum 2 matching words)
  - When user clicks pharmacy marker, rank is looked up and displayed as badge (e.g., "Rank: C")
  - **Provincial Prescription Counts**:
    - IQVIA data for all provinces (AB, BC, MB, NB, NL, PE, NS, ON, QC, SK)
    - Displays average prescription volume based on rank and province
    - Example: Ontario Rank E = 17.3K Rx (average of 0-34,500 range)
    - Shown next to rank badge in pharmacy info card
  - Uses xlsx library to parse Excel file at runtime via @assets import alias
- **Senior Homes**:
  - Custom icon from `attached_assets/Picture2_1762374864085.png`
  - Searchable via type: 'nursing_home'
  - Clickable markers showing SeniorHomeInfoCard with details
  - Included in hide/unhide functionality
  - Displayed in counter with Home icon

**Database Service**:
- **Provider**: Neon (serverless PostgreSQL)
- **Connection**: Via `@neondatabase/serverless` driver
- **Configuration**: `DATABASE_URL` environment variable
- **Purpose**: Persistent storage for user data and potential pharmacy caching

**Development Tools**:
- **Replit Plugins**: Runtime error modal, cartographer (mapping), dev banner
- **Build Tools**: Vite for frontend bundling, esbuild for backend compilation
- **Type Safety**: TypeScript across entire stack with shared types

**UI Framework Dependencies**:
- **Component Library**: Radix UI primitives for accessible, unstyled components
- **Styling**: Tailwind CSS with PostCSS processing
- **Utilities**: clsx and tailwind-merge for className management
- **Form Handling**: React Hook Form with Zod resolvers
- **Icons**: Lucide React icon library

**Session Management** (prepared but not implemented):
- express-session for server-side session handling
- connect-pg-simple for PostgreSQL session storage
- Cookie-based authentication pattern