const express = require('express');
const multer = require('multer');
const path = require('path');
const fs = require('fs');

const app = express();
const PORT = process.env.PORT || 3000;

let isOpen = true; // حالة الإرسال مفتوحة

// إعداد التخزين
const storage = multer.diskStorage({
  destination: 'uploads/',
  filename: (req, file, cb) => {
    const uniqueName = Date.now() + '-' + file.originalname;
    cb(null, uniqueName);
  }
});
const upload = multer({ storage });

// قواعد البيانات
const DB_FILE = 'submissions.json';
const loadSubmissions = () => {
  if (!fs.existsSync(DB_FILE)) return [];
  return JSON.parse(fs.readFileSync(DB_FILE));
};
const saveSubmissions = (data) => {
  fs.writeFileSync(DB_FILE, JSON.stringify(data, null, 2));
};

// تقديم الواجهات والملفات
app.use(express.static('public'));
app.use('/uploads', express.static('uploads'));
app.use(express.json());

// نقطة الحالة
app.get('/status', (req, res) => {
  res.json({ isOpen });
});

// تبديل الحالة
app.post('/toggle', (req, res) => {
  isOpen = !isOpen;
  res.json({ isOpen });
});

// رفع الواجب
app.post('/upload', upload.single('homeworkFile'), (req, res) => {
  if (!isOpen) {
    return res.status(403).send('❌ تم إغلاق الإرسال من قبل المعلم.');
  }
  
  const name = req.body.studentName;
  const file = req.file;
  const time = new Date().toLocaleString("ar-EG");
  
  if (!name || !file) return res.status(400).send('يرجى إدخال الاسم واختيار ملف.');
  
  const submissions = loadSubmissions();
  submissions.push({ name, filename: file.filename, time });
  saveSubmissions(submissions);
  
  res.send('✅ تم إرسال الواجب بنجاح!');
});

// عرض الواجبات
app.get('/submissions', (req, res) => {
  const submissions = loadSubmissions();
  res.json(submissions);
});

// تشغيل الخادم
app.listen(PORT, () => {
  console.log(`✅ الخادم يعمل على http://localhost:${PORT}`);
});