# Medusa Wishlist

A wishlist plugin for Medusa v2 that allows customers to save products for later and share their wishlists with others.

## Features

- 🛍️ **Customer Wishlists** - Each customer gets their own wishlist, automatically created when they add their first item
- ➕ **Add/Update Items** - Customers can add products to their wishlist and update quantities
- 🗑️ **Remove Items** - Easy removal of items from the wishlist
- 🔗 **Share Wishlists** - Generate shareable links using JWT tokens
- 🌐 **Public Access** - Shared wishlists can be viewed without authentication
- 🔒 **Secure** - JWT-based token system for sharing

## Installation

### Option 1: Install as NPM Package

1. Install the plugin:

```bash
npm install @rsc-labs/medusa-wishlist
# or
yarn add @rsc-labs/medusa-wishlist
```

2. Add the plugin to your `medusa-config.js`:

```js
const plugins = [
  // ... other plugins
  {
    resolve: "@rsc-labs/medusa-wishlist",
    options: {
      jwtSecret: process.env.JWT_SECRET || "supersecret"
    }
  }
]
```

Or simply:

```js
const plugins = [
  // ... other plugins
  "@rsc-labs/medusa-wishlist"
]
```

3. Run database migrations:

```bash
npx medusa db:migrate
```

### Option 2: Copy Source Code

You can also copy the source code directly into your Medusa project:

1. Copy the `/src` directory contents into your project
2. Add the module to your `medusa-config.js`:

```js
const modules = [
  // ... other modules
  {
    resolve: "./src/modules/wishlist",
    options: {
      jwtSecret: process.env.JWT_SECRET || "supersecret"
    }
  }
]
```

3. Install required dependencies:

```bash
npm install jsonwebtoken
# or
yarn add jsonwebtoken
```

4. Run database migrations:

```bash
npx medusa db:migrate
```

## Configuration

### JWT Secret

The wishlist sharing feature uses JWT tokens. It's recommended to set a strong secret in your environment variables:

```bash
# .env
JWT_SECRET=your-super-secret-key-here
```

Then configure it in `medusa-config.js`:

```js
{
  resolve: "@rsc-labs/medusa-wishlist",
  options: {
    jwtSecret: process.env.JWT_SECRET
  }
}
```

## API Endpoints

### Authenticated Endpoints (Require Customer Token)

#### Get Customer's Wishlist
```
GET /store/customers/me/wishlist
Authorization: Bearer {customer-token}
```

#### Add or Update Item
```
POST /store/customers/me/wishlist/items
Authorization: Bearer {customer-token}
Content-Type: application/json

{
  "productId": "prod_01XXXXX",
  "productVariantId": "variant_01XXXXX",
  "quantity": 1
}
```

#### Remove Item
```
DELETE /store/customers/me/wishlist/items?productId={productId}&productVariantId={productVariantId}
Authorization: Bearer {customer-token}
```

#### Generate Share Token
```
POST /store/customers/me/wishlist/share-token
Authorization: Bearer {customer-token}

Response:
{
  "shared_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

### Public Endpoints (No Authentication Required)

#### View Shared Wishlist
```
GET /store/wishlists?token={shared-token}
```

## Usage Example

### 1. Customer Login
```bash
curl -X POST http://localhost:9000/store/auth/customer/emailpass \
  -H "Content-Type: application/json" \
  -d '{
    "email": "customer@example.com",
    "password": "password123"
  }'
```

### 2. Add Item to Wishlist
```bash
curl -X POST http://localhost:9000/store/customers/me/wishlist/items \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "productId": "prod_01XXXXX",
    "productVariantId": "variant_01XXXXX",
    "quantity": 1
  }'
```

### 3. Get Wishlist
```bash
curl -X GET http://localhost:9000/store/customers/me/wishlist \
  -H "Authorization: Bearer {token}"
```

### 4. Generate Share Token
```bash
curl -X POST http://localhost:9000/store/customers/me/wishlist/share-token \
  -H "Authorization: Bearer {token}"
```

### 5. View Shared Wishlist (Public)
```bash
curl -X GET "http://localhost:9000/store/wishlists?token={shared-token}"
```

## Database Schema

The plugin creates the following tables:

### `wishlist`
- `id` - Primary key
- `created_at` - Timestamp
- `updated_at` - Timestamp

### `wishlist_item`
- `id` - Primary key
- `wishlist_id` - Foreign key to wishlist
- `product_id` - Product identifier
- `product_variant_id` - Product variant identifier
- `quantity` - Item quantity
- `created_at` - Timestamp
- `updated_at` - Timestamp

### `link_customer_wishlist`
- Links customers to their wishlists (1:1 relationship)

## How It Works

1. **Automatic Creation**: When a customer adds their first item, a wishlist is automatically created and linked to their account
2. **Item Management**: Customers can add, update quantities, or remove items from their wishlist
3. **Sharing**: Customers can generate a JWT token to share their wishlist with others
4. **Public Access**: Anyone with the share token can view the wishlist without authentication

## API Documentation

For detailed API specifications, see the [OpenAPI documentation](./docs/api.yaml).

## Development

### Prerequisites
- Node.js 20+
- Medusa v2
- PostgreSQL

### Local Setup
```bash
# Install dependencies
npm install

# Run migrations
npx medusa db:migrate

# Start development server
npm run dev
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License - see [LICENSE](./LICENSE) file for details.

## Credits

Created by [RSC Labs](https://rsoftcon.com)

## Support

For issues and questions:
- GitHub Issues: [medusa-wishlist/issues](https://github.com/RSC-Labs/medusa-wishlist/issues)
- Documentation: [docs.medusajs.com](https://docs.medusajs.com)

---

**Built for Medusa v2** 🚀