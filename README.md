
npm install
Frontend
- `npm run dev` - Start development server
- `npm run build` - Build for production


Backend
- `npm start` - Start the server
- `npm run dev` - Start with nodemon (development)



Configurable API Endpoints


// PDF Generation
POST /api/generate-pdf


travel/
├── src/
│   ├── components/
│   │   ├── Header.jsx
│   │   ├── Footer.jsx
│   │   └── ItineraryForm.jsx
│   ├── App.jsx
│   └── main.jsx
├── public/
├── index.html
└── package.json
```

Configuration

Update the API endpoint in your frontend configuration:


const API_BASE_URL = 'http://localhost:3000/api';


Environment Variables
Create a `.env` file for backend configuration:

PORT=3000
NODE_ENV=development
