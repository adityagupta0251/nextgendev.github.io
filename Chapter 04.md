---
title: Chapter 04 – Hospital Management System with Express & Mongoose
---

# Chapter 04 – Hospital Management System with Express & Mongoose 🏥

In this chapter, we'll build out a basic Hospital Management System (HMS) backend by:

1. **Bootstrapping an Express server**  
2. **Connecting to MongoDB** via Mongoose  
3. **Defining core data models**: Hospital, Doctor, Patient, MedicalRecord  
4. **Outlining RESTful endpoints** for CRUD operations  

---

## 🚀 1. Express Server Setup (`index.js`)

```js
import express from 'express';
import mongoose from 'mongoose';
import { resolve } from 'path';
import hospitalRoutes from './routes/hospital.js';
import doctorRoutes  from './routes/doctor.js';
import patientRoutes from './routes/patient.js';
import recordRoutes  from './routes/medicalRecord.js';

const app = express();
const port = process.env.PORT || 3010;

// Connect to MongoDB
mongoose
  .connect(process.env.MONGODB_URI || 'mongodb://localhost:27017/hms', {
    useNewUrlParser: true,
    useUnifiedTopology: true
  })
  .then(() => console.log('✅ MongoDB connected'))
  .catch(err => console.error('❌ MongoDB connection error:', err));

// Middleware
app.use(express.json());
app.use(express.static('static'));

// Serve homepage
app.get('/', (req, res) => {
  res.sendFile(resolve(__dirname, 'pages/index.html'));
});

// Mount routes
app.use('/api/hospitals', hospitalRoutes);
app.use('/api/doctors',    doctorRoutes);
app.use('/api/patients',   patientRoutes);
app.use('/api/records',    recordRoutes);

// Start server
app.listen(port, () => {
  console.log(`🚀 HMS server listening at http://localhost:${port}`);
});
````

---

## 🗂 2. Data Models

Place these in `models/`—each file exports a Mongoose model.

### 2.1 Hospital Model (`models/hospital.js`)

```js
import mongoose from 'mongoose';

const hospitalSchema = new mongoose.Schema({
  name:          { type: String, required: true },
  location:      { type: String, required: true },
  contactNumber: { type: String, required: true },
  capacity:      { type: Number, required: true },          // total beds
  departments:   { type: [String], default: [] }           // e.g., ['Cardiology']
}, { timestamps: true });

export const Hospital = mongoose.model('Hospital', hospitalSchema);
```

---

### 2.2 Doctor Model (`models/doctor.js`)

```js
import mongoose from 'mongoose';

const doctorSchema = new mongoose.Schema({
  name:               { type: String, required: true },
  specialization:     { type: String, required: true },
  experience:         { type: Number, required: true },     // in years
  gender:             { type: String, enum: ['M','F','O'], required: true },
  contact:            { type: String, required: true },
  address:            { type: String, required: true },
  affiliatedHospital: { type: mongoose.Schema.Types.ObjectId, ref: 'Hospital' }
}, { timestamps: true });

export const Doctor = mongoose.model('Doctor', doctorSchema);
```

---

### 2.3 Patient Model (`models/patient.js`)

```js
import mongoose from 'mongoose';

const patientSchema = new mongoose.Schema({
  name:         { type: String, required: true },
  age:          { type: Number, required: true },
  gender:       { type: String, enum: ['M','F','O'], required: true },
  bloodGroup:   { type: String, required: true },
  address:      { type: String, required: true },
  diagnosis:    { type: String, required: true },
  admittedIn:   { type: mongoose.Schema.Types.ObjectId, ref: 'Hospital' }
}, { timestamps: true });

export const Patient = mongoose.model('Patient', patientSchema);
```

---

### 2.4 MedicalRecord Model (`models/medicalRecord.js`)

```js
import mongoose from 'mongoose';

const medicalRecordSchema = new mongoose.Schema({
  patient:     { type: mongoose.Schema.Types.ObjectId, ref: 'Patient', required: true },
  doctor:      { type: mongoose.Schema.Types.ObjectId, ref: 'Doctor',  required: true },
  diagnosis:   { type: String, required: true },
  treatment:   { type: String, required: true },
  medications: { type: [String], default: [] },
  visitDate:   { type: Date, default: Date.now },
  notes:       { type: String }
}, { timestamps: true });

export const MedicalRecord = mongoose.model('MedicalRecord', medicalRecordSchema);
```

---

## 🔗 3. RESTful Routes (outline)

Create route files in `routes/` that import the corresponding model and expose CRUD endpoints. Example for hospitals:

```js
// routes/hospital.js
import express from 'express';
import { Hospital } from '../models/hospital.js';

const router = express.Router();

// Create hospital
router.post('/', async (req, res) => {
  const h = new Hospital(req.body);
  const saved = await h.save();
  res.status(201).json(saved);
});

// Read all
router.get('/', async (_, res) => {
  const list = await Hospital.find();
  res.json(list);
});

// Read one
router.get('/:id', async (req, res) => {
  const h = await Hospital.findById(req.params.id);
  res.json(h);
});

// Update
router.put('/:id', async (req, res) => {
  const updated = await Hospital.findByIdAndUpdate(req.params.id, req.body, { new: true });
  res.json(updated);
});

// Delete
router.delete('/:id', async (req, res) => {
  await Hospital.findByIdAndDelete(req.params.id);
  res.sendStatus(204);
});

export default router;
```

Repeat similar patterns for **doctor**, **patient**, and **medicalRecord** routes.

---

## 🌱 4. Next Steps

* **Validation & Sanitization**: Use `express-validator` or Joi to enforce data integrity.
* **Authentication & Authorization**: Secure endpoints with JWT or session-based auth.
* **Error Handling**: Centralize error middleware for consistent API responses.
* **Pagination & Filtering**: Implement query parameters for large datasets.
* **Testing**: Write unit and integration tests (Jest, Supertest).
* **Deployment**: Containerize with Docker and deploy to cloud (Heroku, AWS, etc.).

---

🎉 You now have a robust foundation for your Hospital Management System: an Express server, connected models, and outlined routes to manage hospitals, doctors, patients, and medical records. Onward to Chapter 05, where we'll implement authentication and role-based access control!

```
```
