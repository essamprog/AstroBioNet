# AstroNile Pro - نظام البحث الذكي للأبحاث العلمية

## نظرة عامة على المشروع

AstroNile Pro هو نظام شامل للبحث والتحليل في الأبحاث العلمية المتعلقة بعلوم الفضاء والفيزياء الحيوية. يتضمن النظام عدة وحدات متكاملة تهدف إلى توفير تجربة بحث وتحليل متقدمة للمستخدمين.

## المميزات الرئيسية

- 🔍 **نظام البحث الذكي**: بحث متقدم في قاعدة بيانات الأبحاث العلمية
- 🤖 **مساعد ذكي (RAG)**: إجابات ذكية مبنية على الأبحاث المتوفرة
- 📊 **لوحة تحكم تحليلية**: إحصائيات وتصورات تفاعلية للبيانات
- 🕸️ **رسم بياني للمعرفة**: تمثيل بصري للعلاقات بين الأبحاث

## التقنيات والمكتبات المستخدمة

### Backend (Python)
- **Flask 2.3.3**: إطار عمل ويب خفيف وسريع
- **Flask-CORS 4.0.0**: معالجة طلبات CORS للواجهة الأمامية
- **ChromaDB 0.4.15**: قاعدة بيانات متجهة للبحث الدلالي
- **Sentence-Transformers 2.2.2**: تحويل النصوص إلى متجهات دلالية
- **NumPy 1.24.3**: معالجة البيانات الرقمية
- **PyTorch 2.0.1**: إطار عمل التعلم الآلي
- **Transformers 4.33.2**: نماذج معالجة اللغة الطبيعية

### Frontend (HTML/CSS/JavaScript)
- **Tailwind CSS 4.1.13**: إطار عمل CSS للتصميم السريع
- **Chart.js**: مكتبة الرسوم البيانية التفاعلية
- **Vis.js Network**: مكتبة رسم الشبكات والرسوم البيانية
- **Font Awesome 6.0.0**: مكتبة الأيقونات
- **Boxicons 2.1.4**: مكتبة أيقونات إضافية
- **Google Fonts (Inter, Cairo)**: خطوط ويب عالية الجودة

### قواعد البيانات والملفات
- **ChromaDB**: قاعدة بيانات متجهة محلية
- **JSON Files**: 595 ملف بيانات للأبحاث العلمية
- **CSV Dataset**: ملف بيانات مصنف للأبحاث
- **Vector Database**: قاعدة بيانات متجهة مع 600+ بحث علمي

## هيكل المشروع

```
AstroNle_Pro_Final/
├── public/
│   ├── astro_agent/          # نظام المساعد الذكي
│   │   ├── server.py         # خادم Flask الرئيسي
│   │   ├── agentai.html      # واجهة المساعد
│   │   ├── script.js         # منطق الواجهة الأمامية
│   │   ├── style.css         # تنسيقات CSS
│   │   ├── my_vector_db/     # قاعدة البيانات المتجهة
│   │   └── json_files/       # ملفات البيانات (595 ملف)
│   ├── knowledge_graph_pro/  # رسم بياني للمعرفة
│   │   ├── knowledge_g.html  # واجهة الرسم البياني
│   │   ├── data.json         # بيانات الأبحاث
│   │   ├── edges.json        # روابط العلاقات
│   │   └── script.js         # منطق الرسم البياني
│   ├── searchpro/           # نظام البحث المتقدم
│   │   ├── pagsearch.html   # واجهة البحث
│   │   ├── data.json        # بيانات البحث
│   │   └── details.html     # صفحة التفاصيل
│   ├── dashboard/           # لوحة التحكم
│   │   ├── index.html       # واجهة اللوحة
│   │   └── dashboard.json   # بيانات الإحصائيات
│   └── css/                 # ملفات التنسيق العامة
├── src/
│   └── input.css            # ملف CSS الرئيسي
├── dist/
│   └── output.css           # ملف CSS المُحسن
├── index.html               # الصفحة الرئيسية
├── package.json             # إعدادات Node.js
└── requirements.txt         # متطلبات Python
```


### 1. تشغيل الخادم

```bash
# تشغيل خادم Flask
cd public/astro_agent
python server.py

# تشغيل Tailwind CSS (في terminal منفصل)
npm run dev
```

### 2. فتح التطبيق

افتح `index.html` في المتصفح أو استخدم خادم محلي:

```bash
# باستخدام Python
python -m http.server 8000

# أو باستخدام Node.js
npx serve .
```

### نظام RAG (Retrieval-Augmented Generation)

```python
class SimpleRAGModel:
    def generate_answer(self, context, question):
        # تحليل السؤال والبحث عن الكلمات المفتاحية
        question_lower = question.lower()
        sentences = context.split('.')
        relevant_sentences = []
        
        for sentence in sentences:
            sentence_lower = sentence.lower()
            matches = sum(1 for word in question_words if word in sentence_lower)
            if matches > 0 and len(sentence.strip()) > 10:
                relevant_sentences.append((sentence.strip(), matches))
        
        # ترتيب الجمل حسب الصلة
        relevant_sentences.sort(key=lambda x: x[1], reverse=True)
        
        # بناء الإجابة من أهم 3 جمل
        answer = "بناءً على المصادر المتاحة:\n\n"
        for sentence, _ in relevant_sentences[:3]:
            if sentence:
                answer += f"• {sentence}.\n"
        
        return answer
```

### البحث في قاعدة البيانات المتجهة

```python
def retrieve_context(query, n_results=5):
    try:
        # تحويل السؤال إلى متجه
        query_embedding = embedding_model.encode([query]).tolist()
        
        # البحث في قاعدة البيانات
        results = collection.query(
            query_embeddings=query_embedding,
            n_results=min(n_results, collection.count())
        )
        
        return results['documents'][0] if results['documents'] else ["لا توجد مستندات ذات صلة"]
    except Exception as e:
        return ["Error retrieving information."]
```

### واجهة البحث التفاعلية

```javascript
// إرسال طلب البحث
const sendMessage = async (message) => {
    try {
        const response = await fetch(API_REQUEST_URL, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({ message: message })
        });
        
        const data = await response.json();
        displayResponse(data.response, data.sources_count);
    } catch (error) {
        console.error('Error:', error);
    }
};
```

## البيانات المستخدمة

- **600+ بحث علمي** في مجالات علوم الفضاء والفيزياء الحيوية
- **تصنيف تلقائي** للأبحاث حسب المجال العلمي
- **تحليل الكلمات المفتاحية** لكل بحث
- **روابط مباشرة** لمصادر الأبحاث الأصلية
- **ملخصات ونتائج** مفصلة لكل بحث

## API Endpoints

- `POST /api/chat` - إرسال سؤال والحصول على إجابة ذكية
- `GET /api/info` - معلومات قاعدة البيانات والإحصائيات
- `GET /health` - فحص صحة النظام

## التخصيص والتطوير

يمكن تخصيص النظام من خلال:
- تعديل أسئلة الاقتراحات في الواجهة
- تغيير ألوان وتصميم الواجهة
- إضافة مجالات بحث جديدة
- تحسين خوارزميات البحث والتحليل
