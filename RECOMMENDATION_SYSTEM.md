# Food Recommendation System

A smart, real-time food recommendation system for the QR-based restaurant ordering app using TensorFlow.js and collaborative filtering.

## 🚀 Features

### Core Functionality
- **Real-time Recommendations**: Get personalized food suggestions based on current cart items
- **Collaborative Filtering**: Neural network model trained on historical order data
- **Content-based Filtering**: Recommendations based on item features (category, price, dietary preferences)
- **User Personalization**: Learn from user order history and preferences
- **Smart Fallbacks**: Popular items when no cart items or insufficient data

### Technical Features
- **TensorFlow.js Integration**: Neural network for learning item relationships
- **Multi-hot Encoding**: Efficient representation of order data
- **Co-occurrence Analysis**: Statistical analysis of item combinations
- **Real-time Updates**: Automatic model retraining and preference updates
- **Admin Dashboard**: Monitor system performance and analytics

## 🏗️ Architecture

### Backend Components

#### 1. Database Models (`server/database/models/recommendation-model.js`)
- **ItemEmbedding**: Stores learned vector representations of menu items
- **CoOccurrence**: Tracks statistical relationships between items
- **ModelMetadata**: Stores model configuration and training history
- **UserPreference**: User behavior and preference data

#### 2. Recommendation Engine (`server/utils/recommendation-engine.js`)
- **Neural Network**: 4-layer dense network for collaborative filtering
- **Training Pipeline**: Automatic data preparation and model training
- **Real-time Inference**: Fast recommendation generation
- **Embedding Management**: Item vector generation and storage

#### 3. API Controllers (`server/controllers/recommendation-controller.js`)
- **Cart-based Recommendations**: `POST /api/recommendations/cart`
- **Popular Items**: `GET /api/recommendations/popular`
- **User Recommendations**: `GET /api/recommendations/user`
- **Similar Items**: `GET /api/recommendations/similar/:itemId`
- **Admin Functions**: Model retraining, status monitoring, analytics

### Frontend Components

#### 1. Client Interface (`client/src/components/client/Recommendations.jsx`)
- **Smart Picks Tab**: Cart-based and personalized recommendations
- **Popular Items Tab**: Most ordered items
- **Real-time Updates**: Automatic refresh when cart changes
- **Visual Indicators**: Recommendation scores and item badges

#### 2. Admin Dashboard (`client/src/components/admin/RecommendationDashboard.jsx`)
- **Model Status Monitoring**: Training status, accuracy metrics
- **Analytics Dashboard**: Top combinations, popular items, category distribution
- **Model Management**: Retrain model, view configuration
- **Performance Metrics**: Order statistics, user engagement

## 🔧 Installation & Setup

### Prerequisites
- Node.js 16+
- MongoDB
- Redis (for rate limiting)

### Backend Setup

1. **Install Dependencies**
```bash
cd server
npm install
```

2. **Environment Variables**
```env
# Add to your .env file
MONGODB_URI=your_mongodb_connection_string
REDIS_URL=your_redis_connection_string
```

3. **Initialize the System**
```bash
# Start the server
npm run dev
```

The recommendation engine will automatically:
- Initialize on server startup
- Train the model if no existing model is found
- Update co-occurrence statistics
- Generate item embeddings

### Frontend Integration

1. **Import the Components**
```jsx
import Recommendations from './components/client/Recommendations';
import RecommendationDashboard from './components/admin/RecommendationDashboard';
```

2. **Use in Client Interface**
```jsx
<Recommendations 
  cartItems={cartItems}
  onAddToCart={handleAddToCart}
  userId={currentUser?.id}
/>
```

3. **Use in Admin Interface**
```jsx
<RecommendationDashboard />
```

## 📊 API Endpoints

### Public Endpoints

#### Get Cart-based Recommendations
```http
POST /api/recommendations/cart
Content-Type: application/json

{
  "cartItems": ["itemId1", "itemId2"],
  "limit": 5
}
```

#### Get Popular Items
```http
GET /api/recommendations/popular?limit=10&category=Main Course
```

#### Get User Recommendations
```http
GET /api/recommendations/user?limit=5
Authorization: Bearer <token>
```

#### Get Similar Items
```http
GET /api/recommendations/similar/itemId?limit=5
```

### Admin Endpoints

#### Retrain Model
```http
POST /api/recommendations/retrain
Authorization: Bearer <admin_token>
```

#### Get Model Status
```http
GET /api/recommendations/status
Authorization: Bearer <admin_token>
```

#### Get Analytics
```http
GET /api/recommendations/analytics
Authorization: Bearer <admin_token>
```

## 🧠 How It Works

### 1. Data Collection
- **Order History**: Analyzes completed orders from the last 6 months
- **User Behavior**: Tracks individual user preferences and ordering patterns
- **Item Features**: Uses menu item attributes (category, price, dietary info)

### 2. Model Training
- **Multi-hot Encoding**: Converts orders to binary vectors
- **Neural Network**: 4-layer architecture with dropout for regularization
- **Loss Function**: Binary cross-entropy for multi-label prediction
- **Training**: 50 epochs with validation split

### 3. Recommendation Generation
- **Input Processing**: Converts cart items to multi-hot vector
- **Model Inference**: Neural network predicts item probabilities
- **Filtering**: Removes items already in cart
- **Scoring**: Ranks recommendations by prediction confidence
- **Personalization**: Applies user preference filters

### 4. Real-time Updates
- **Order Completion**: Automatically updates user preferences
- **Model Retraining**: Background process for improved accuracy
- **Statistics Update**: Refreshes co-occurrence and popularity data

## 🎯 Usage Examples

### Basic Integration
```jsx
// In your menu/cart component
const [recommendations, setRecommendations] = useState([]);

useEffect(() => {
  if (cartItems.length > 0) {
    fetchRecommendations();
  }
}, [cartItems]);

const fetchRecommendations = async () => {
  const response = await axios.post('/api/recommendations/cart', {
    cartItems: cartItems.map(item => item.id)
  });
  setRecommendations(response.data.data);
};
```

### Advanced Usage with User Context
```jsx
// Personalized recommendations
const [userRecommendations, setUserRecommendations] = useState([]);

useEffect(() => {
  if (user && cartItems.length === 0) {
    fetchUserRecommendations();
  }
}, [user, cartItems]);

const fetchUserRecommendations = async () => {
  const response = await axios.get('/api/recommendations/user', {
    headers: { Authorization: `Bearer ${token}` }
  });
  setUserRecommendations(response.data.data);
};
```

## 📈 Performance & Optimization

### Model Performance
- **Training Time**: ~2-5 minutes for 1000+ orders
- **Inference Speed**: <100ms for recommendations
- **Memory Usage**: ~50MB for model and embeddings
- **Accuracy**: Improves with more training data

### Optimization Strategies
- **Batch Processing**: Efficient tensor operations
- **Indexing**: Database indexes for fast queries
- **Caching**: Redis for frequently accessed data
- **Background Updates**: Non-blocking preference updates

### Scalability
- **Horizontal Scaling**: Stateless API design
- **Database Sharding**: Separate recommendation collections
- **CDN Integration**: Static asset optimization
- **Load Balancing**: Multiple server instances

## 🔍 Monitoring & Analytics

### Key Metrics
- **Recommendation Accuracy**: Click-through rates
- **Model Performance**: Training loss, validation accuracy
- **User Engagement**: Recommendation usage patterns
- **Business Impact**: Increased order values, item discovery

### Admin Dashboard Features
- **Real-time Monitoring**: Model status, training progress
- **Analytics Visualization**: Top combinations, popular items
- **Performance Metrics**: Response times, error rates
- **User Insights**: Preference patterns, ordering behavior

## 🛠️ Customization

### Model Configuration
```javascript
// In recommendation-engine.js
const modelConfig = {
  embeddingDimension: 32,    // Vector size
  epochs: 50,                // Training iterations
  batchSize: 32,             // Batch size
  learningRate: 0.001,       // Learning rate
  dropoutRate: 0.3           // Dropout for regularization
};
```

### Feature Engineering
```javascript
// Add custom features to item embeddings
const customFeatures = {
  seasonal: isSeasonalItem(item),
  trending: getTrendingScore(item),
  profitMargin: calculateProfitMargin(item)
};
```

### Recommendation Algorithms
```javascript
// Implement custom recommendation strategies
const customRecommendation = {
  hybrid: combineCollaborativeAndContentBased(),
  contextual: addTimeAndLocationContext(),
  diversity: ensureRecommendationDiversity()
};
```

## 🚨 Troubleshooting

### Common Issues

#### Model Not Training
- Check MongoDB connection
- Verify order data exists
- Check TensorFlow.js installation
- Review server logs for errors

#### Poor Recommendations
- Ensure sufficient training data (100+ orders minimum)
- Check item availability status
- Verify user preference data
- Retrain model with more data

#### Performance Issues
- Monitor memory usage
- Check database query performance
- Optimize tensor operations
- Implement caching strategies

### Debug Commands
```bash
# Check model status
curl -X GET http://localhost:5000/api/recommendations/status

# Force model retraining
curl -X POST http://localhost:5000/api/recommendations/retrain

# Test recommendations
curl -X POST http://localhost:5000/api/recommendations/cart \
  -H "Content-Type: application/json" \
  -d '{"cartItems": ["itemId1"], "limit": 5}'
```

## 🔮 Future Enhancements

### Planned Features
- **A/B Testing**: Compare recommendation strategies
- **Contextual Recommendations**: Time, weather, location-based
- **Multi-modal Learning**: Image and text-based features
- **Real-time Learning**: Online model updates
- **Explainable AI**: Recommendation reasoning

### Advanced Algorithms
- **Graph Neural Networks**: Complex item relationships
- **Transformer Models**: Sequence-based recommendations
- **Reinforcement Learning**: Adaptive recommendation strategies
- **Federated Learning**: Privacy-preserving training

## 📚 Resources

### Documentation
- [TensorFlow.js Guide](https://www.tensorflow.org/js/guide)
- [MongoDB Aggregation](https://docs.mongodb.com/manual/aggregation/)
- [Express.js Best Practices](https://expressjs.com/en/advanced/best-practices-performance.html)

### Research Papers
- "Neural Collaborative Filtering" - He et al.
- "Deep Learning based Recommender System" - Zhang et al.
- "Session-based Recommendations" - Hidasi et al.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Implement your changes
4. Add tests and documentation
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

[![](https://mermaid.ink/img/pako:eNqtV21PGkEQ_iubTdpogggIHNDGBAUtiTQEsE0s_bDcjXD1bve6uyeg-N87u8ebaCqH3qd7mWee2Xm_R-oKD2iNjiSLxqTfGPABJ3gVq5WTXwN6rUB-HcrT5lSD5CwgdVcLOaC_EykVDxNgsVouo_hKrDdTGkK1ElyozKFMh81C4JrUOy1lVHfZg5ARm2UIaDe7jcgjoh7rMSJ8l2lf8BXwUohRAK_DCghrMM2GTIEVbgs-Eo2zDOmC56_tAu69PImD4DPm3uFHQ2bg39FL2T-KfGZh9IU0p5EEtX06p4i4b_1-ZyH1E4Y9gVo06YG8T_y41HOg7Cu8PdzWUjIn7rRIV8ToTJV43_Kh9LawcXoX_sagNGn7nhfAhEn4P8ZZEJwLrqUIggXJwrRt6YplcEWIQfOSADT5yOeWpA9cCXkRiMkryOpGDDBvXLSGXLFZ4ggbD6HgGejTJ9L0RqAI00SPfUUCuIeAHIykiCPwyHBGlIilC4cf43dydHQ6b4uYazVP7fq3hK1yrJ9E9T6B2hFjiS6ExI-eIlrM0wf5TWnLcQlaEfksFxS5lSKcvy9RdmPvAvPU8UT6GhMECRi599k8faK9WvLGeMwbUo-iYNFnbG8C5upFWv1A4i3Ly4YZIZgDYimWpMMafIDf8RTTF3WeS7BLOtJho6RVJbhzEUaCY9t74S7TEi-A6VjChtQuSNMVr1trAXLlDyWTs12wZhr0NNNA2oyjqaaHG9xNrDTj3rEJHkxtW_-Amk7r2UWGcA9zZ_4u76YBPi_x_eOyP-e-UUlrrmU9i_0AO8zE1-P53um0Kr-0ifHWRmJNbOFxJTKvrdyvRvfyT5vd4XFMJ3NZEKxa8W5TJXUwNygZLkh7cr5vdqJFHIy3tSC3QhJMS2WmwjzNDrbzBOhIYXo7HjhKdki1HAFpFsud6TbWTlgzpV1IU8ynbZfu7EWawf3d92hNyxgyNAQZMvNIH40NA4rWhkhQw1uPybsBHfAnxESM3wgRLmFYd6Mxrd2yQOFTHOGMhYbPcEaGq7fStthzszLRWr5Uskpo7ZFO8bFczBacglMsl06cnJPPlzN0RmvlQrZ6gi_zxXyu4lQKpacMfbC0uWzFQQ14DKzgdvIfYn9Hnv4Bul8QOg?type=png)](https://mermaid.live/edit#pako:eNqtV21PGkEQ_iubTdpogggIHNDGBAUtiTQEsE0s_bDcjXD1bve6uyeg-N87u8ebaCqH3qd7mWee2Xm_R-oKD2iNjiSLxqTfGPABJ3gVq5WTXwN6rUB-HcrT5lSD5CwgdVcLOaC_EykVDxNgsVouo_hKrDdTGkK1ElyozKFMh81C4JrUOy1lVHfZg5ARm2UIaDe7jcgjoh7rMSJ8l2lf8BXwUohRAK_DCghrMM2GTIEVbgs-Eo2zDOmC56_tAu69PImD4DPm3uFHQ2bg39FL2T-KfGZh9IU0p5EEtX06p4i4b_1-ZyH1E4Y9gVo06YG8T_y41HOg7Cu8PdzWUjIn7rRIV8ToTJV43_Kh9LawcXoX_sagNGn7nhfAhEn4P8ZZEJwLrqUIggXJwrRt6YplcEWIQfOSADT5yOeWpA9cCXkRiMkryOpGDDBvXLSGXLFZ4ggbD6HgGejTJ9L0RqAI00SPfUUCuIeAHIykiCPwyHBGlIilC4cf43dydHQ6b4uYazVP7fq3hK1yrJ9E9T6B2hFjiS6ExI-eIlrM0wf5TWnLcQlaEfksFxS5lSKcvy9RdmPvAvPU8UT6GhMECRi599k8faK9WvLGeMwbUo-iYNFnbG8C5upFWv1A4i3Ly4YZIZgDYimWpMMafIDf8RTTF3WeS7BLOtJho6RVJbhzEUaCY9t74S7TEi-A6VjChtQuSNMVr1trAXLlDyWTs12wZhr0NNNA2oyjqaaHG9xNrDTj3rEJHkxtW_-Amk7r2UWGcA9zZ_4u76YBPi_x_eOyP-e-UUlrrmU9i_0AO8zE1-P53um0Kr-0ifHWRmJNbOFxJTKvrdyvRvfyT5vd4XFMJ3NZEKxa8W5TJXUwNygZLkh7cr5vdqJFHIy3tSC3QhJMS2WmwjzNDrbzBOhIYXo7HjhKdki1HAFpFsud6TbWTlgzpV1IU8ynbZfu7EWawf3d92hNyxgyNAQZMvNIH40NA4rWhkhQw1uPybsBHfAnxESM3wgRLmFYd6Mxrd2yQOFTHOGMhYbPcEaGq7fStthzszLRWr5Uskpo7ZFO8bFczBacglMsl06cnJPPlzN0RmvlQrZ6gi_zxXyu4lQKpacMfbC0uWzFQQ14DKzgdvIfYn9Hnv4Bul8QOg)

**Note**: This recommendation system is designed for production use but should be thoroughly tested in your specific environment before deployment. 