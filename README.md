# Express.js REST API with MongoDB and JWT Authentication

A complete RESTful API built with Express.js, MongoDB, and JWT authentication featuring user management and post operations.

## Features

- 🔐 **JWT Authentication** - Secure user authentication and authorization
- 👥 **User Management** - User registration, login, profile management
- 📝 **Post Operations** - Full CRUD operations for posts
- 💬 **Comments System** - Add and manage comments on posts
- ❤️ **Like System** - Like/unlike posts functionality
- 🔍 **Advanced Filtering** - Search, filter, and sort posts
- 📄 **Pagination** - Efficient data pagination
- 🛡️ **Security** - Rate limiting, input validation, error handling
- 👑 **Role-based Access** - Admin and user roles

## Tech Stack

- **Backend:** Node.js, Express.js
- **Database:** MongoDB with Mongoose ODM
- **Authentication:** JWT (JSON Web Tokens)
- **Validation:** Express-validator
- **Security:** Helmet, CORS, Rate limiting
- **Password Hashing:** bcryptjs

## Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd express-rest-api
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Set up environment variables**
   ```bash
   cp .env.example .env
   ```
   
   Edit `.env` file with your configuration:
   ```
   PORT=5000
   NODE_ENV=development
   MONGODB_URI=mongodb://localhost:27017/express_rest_api
   JWT_SECRET=your_super_secret_jwt_key_here_change_this_in_production
   JWT_EXPIRE=30d
   ```

4. **Start MongoDB**
   Make sure MongoDB is running on your system.

5. **Run the application**
   ```bash
   # Development mode
   npm run dev
   
   # Production mode
   npm start
   ```

The API will be available at `http://localhost:5000`

## API Endpoints

### Authentication Routes (`/api/auth`)

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| POST | `/register` | Register a new user | Public |
| POST | `/login` | Login user | Public |
| GET | `/me` | Get current user profile | Private |
| PUT | `/me` | Update user profile | Private |
| PUT | `/updatepassword` | Update user password | Private |

### User Routes (`/api/users`)

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/` | Get all users (admin only) | Private/Admin |
| GET | `/:id` | Get user by ID | Public |
| GET | `/:id/posts` | Get user's posts | Public |
| PUT | `/:id` | Update user (admin only) | Private/Admin |
| DELETE | `/:id` | Delete user (admin only) | Private/Admin |

### Post Routes (`/api/posts`)

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/` | Get all published posts | Public |
| GET | `/:id` | Get single post | Public |
| POST | `/` | Create new post | Private |
| PUT | `/:id` | Update post | Private |
| DELETE | `/:id` | Delete post | Private |
| PUT | `/:id/like` | Like/unlike post | Private |
| POST | `/:id/comments` | Add comment to post | Private |
| DELETE | `/:id/comments/:commentId` | Delete comment | Private |
| GET | `/my/posts` | Get current user's posts | Private |
| GET | `/category/:category` | Get posts by category | Public |

## Request Examples

### Register User
```bash
POST /api/auth/register
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "Password123",
  "bio": "Software developer"
}
```

### Login User
```bash
POST /api/auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "Password123"
}
```

### Create Post
```bash
POST /api/posts
Authorization: Bearer <your-jwt-token>
Content-Type: application/json

{
  "title": "My First Post",
  "content": "This is the content of my first post...",
  "category": "Technology",
  "tags": ["javascript", "nodejs", "express"],
  "status": "published"
}
```

### Get Posts with Filters
```bash
GET /api/posts?page=1&limit=10&category=Technology&sortBy=popular&search=javascript
```

## Query Parameters

### Posts Filtering
- `page` - Page number (default: 1)
- `limit` - Items per page (default: 10)
- `category` - Filter by category
- `tags` - Filter by tags (comma-separated)
- `search` - Search in title and content
- `author` - Filter by author ID
- `sortBy` - Sort by: `newest` (default), `oldest`, `popular`, `mostLiked`, `title`

### User Filtering (Admin only)
- `page` - Page number
- `limit` - Items per page
- `role` - Filter by role
- `isActive` - Filter by active status
- `search` - Search in name and email

## Response Format

All API responses follow this format:

```json
{
  "success": true|false,
  "message": "Response message",
  "data": {...}, // Response data
  "pagination": {...}, // Pagination info (when applicable)
  "errors": [...] // Validation errors (when applicable)
}
```

## Error Handling

The API includes comprehensive error handling:

- **400** - Bad Request (validation errors)
- **401** - Unauthorized (authentication required)
- **403** - Forbidden (insufficient permissions)
- **404** - Not Found
- **500** - Internal Server Error

## Security Features

- Password hashing with bcryptjs
- JWT token authentication
- Rate limiting (100 requests per 15 minutes)
- Input validation and sanitization
- CORS protection
- Helmet security headers
- MongoDB injection protection

## Data Models

### User Model
```javascript
{
  name: String (required, 2-50 chars),
  email: String (required, unique, valid email),
  password: String (required, min 6 chars, hashed),
  avatar: String (optional),
  bio: String (optional, max 500 chars),
  role: String (enum: 'user', 'admin', default: 'user'),
  isActive: Boolean (default: true),
  lastLogin: Date,
  createdAt: Date,
  updatedAt: Date
}
```

### Post Model
```javascript
{
  title: String (required, max 200 chars),
  content: String (required, max 5000 chars),
  excerpt: String (auto-generated if not provided),
  author: ObjectId (ref: User, required),
  tags: [String] (max 30 chars each),
  category: String (max 50 chars),
  status: String (enum: 'draft', 'published', 'archived'),
  featuredImage: String,
  likes: [{ user: ObjectId, createdAt: Date }],
  comments: [{ user: ObjectId, content: String, createdAt: Date }],
  viewCount: Number (default: 0),
  publishedAt: Date,
  createdAt: Date,
  updatedAt: Date
}
```

## Development

### Prerequisites
- Node.js (v14 or higher)
- MongoDB (v4.4 or higher)
- npm or yarn

### Scripts
```bash
npm start        # Start production server
npm run dev      # Start development server with nodemon
npm test         # Run tests (not implemented)
```

### Project Structure
```
├── models/              # Database models
│   ├── User.js
│   └── Post.js
├── routes/              # API routes
│   ├── auth.js
│   ├── users.js
│   └── posts.js
├── middleware/          # Custom middleware
│   ├── auth.js
│   ├── errorHandler.js
│   └── validation.js
├── utils/               # Utility functions
│   └── jwt.js
├── .env                 # Environment variables
├── .env.example         # Environment template
├── server.js            # Main application file
└── package.json         # Dependencies and scripts
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

For support or questions, please open an issue in the repository.