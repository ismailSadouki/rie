
بالطبع، إليك الترجمة إلى العربية مع إبقاء الكلمات المهمة داخل () بالإنجليزية كما طلبت:

---

# مقدمة في (Machine Learning)

امتحان الفصل الأول (Online Exam)  
السنة الرابعة (Statistics and Data Science)

أيوب عسري (Ayoub Asri)

27 يناير 2026

اختبار الفصل الأول (First Semester Test)

**السياق (Context)**

يمثل مرض السكري (Diabetes mellitus) أحد أكبر التحديات الصحية العالمية في القرن الواحد والعشرين، حيث يؤثر على أكثر من 500 مليون بالغ حول العالم ويساهم في ملايين الوفيات سنويًا. هذا الاضطراب الأيضي المزمن لا يقلل فقط من جودة الحياة، بل يضع أيضًا ضغطًا هائلًا على نظم الرعاية الصحية، مع مضاعفات مرتبطة بالسكري تشمل أمراض القلب والأوعية الدموية (cardiovascular disease)، فشل الكلى (kidney failure)، العمى (blindness)، وبتر الأطراف السفلية (lower limb amputations)، والتي تمثل عبئًا كبيرًا من حيث المرض والوفاة. العبء الاقتصادي هائل، حيث تتجاوز النفقات العالمية على الرعاية الصحية المتعلقة بالسكري (diabetes) مئات المليارات من الدولارات سنويًا. علاوة على ذلك، يستمر انتشار السكري في الارتفاع بمعدل مقلق، نتيجة الشيخوخة السكانية (aging populations)، والتحضر (urbanization)، وأنماط الحياة الكسولة (sedentary lifestyles)، وانتشار السمنة (obesity epidemic).

الكشف المبكر (early detection) والتدخل (intervention) أمران حاسمان في إدارة السكري ومنع مضاعفاته المدمرة. ومع ذلك، غالبًا ما تفشل الطرق التقليدية للفحص (traditional screening approaches) في تحديد الأفراد المعرضين للخطر حتى يحدث خلل أيضي كبير بالفعل. وهنا يأتي دور (machine learning) و(artificial intelligence) في تقديم إمكانيات تحويلية. من خلال تحليل الأنماط المعقدة عبر عوامل خطر متعددة—including (demographic characteristics)، (lifestyle behaviors)، (clinical measurements)، و(genetic predispositions)—يمكن لنماذج (machine learning) تحديد العلاقات الدقيقة وغير الخطية (non-linear relationships) التي قد تكون غير مرئية للأطباء البشريين أو الطرق الإحصائية التقليدية. هذه النماذج التنبؤية (predictive models) يمكن أن تصنف السكان حسب مستوى الخطر (risk level)، مما يتيح برامج فحص مستهدفة واستراتيجيات وقائية شخصية (personalized prevention strategies) يمكنها اعتراض السكري قبل التشخيص السريري (clinical diagnosis).

تطبيق (machine learning) في التنبؤ بالسكري وإدارته (diabetes prediction and management) جذاب بشكل خاص لأن خطر السكري متعدد العوامل (multifactorial) ويتضمن تفاعلات معقدة بين العوامل البيولوجية (biological)، والسلوكية (behavioral)، والبيئية (environmental). يمكن للخوارزميات المتقدمة مثل طرق (ensemble methods)، الشبكات العصبية (neural networks)، و(gradient boosting) أن تلتقط هذه التفاعلات المعقدة وتوفر تقييمات دقيقة للخطر أكثر من أنظمة التقييم التقليدية (conventional scoring systems). علاوة على ذلك، يمكن تحسين نماذج (machine learning) باستمرار مع توافر بيانات جديدة، مما يزيد من دقة التنبؤ مع مرور الوقت. إدماج هذه النماذج في أنظمة دعم القرار السريري (clinical decision support systems) له القدرة على تحويل الرعاية الوقائية، وتحويل الرعاية الصحية من العلاج التفاعلي (reactive treatment) إلى تقليل المخاطر الاستباقي (proactive risk mitigation).

إلى جانب التنبؤ، تمتد تطبيقات (machine learning) في السكري لتشمل تحسين العلاج الشخصي (personalized treatment optimization)، تحليل مراقبة الجلوكوز المستمرة (continuous glucose monitoring analysis)، خوارزميات تحديد جرعات الأدوية (medication dosing algorithms)، واكتشاف مؤشرات حيوية جديدة (novel biomarkers) من خلال تحليل أهمية السمات (feature importance analysis). يخلق التقاء البيانات الضخمة (big data)، الأجهزة الصحية القابلة للارتداء (wearable health devices)، السجلات الصحية الإلكترونية (electronic health records)، والتقنيات التحليلية المتقدمة (sophisticated analytical techniques) فرصًا غير مسبوقة لفهم السكري على المستويين السكاني والفردي. توفر هذه المجموعة من البيانات (dataset) بوابة لاستكشاف هذه الإمكانيات، مما يمنح الباحثين (researchers)، علماء البيانات (data scientists)، والمهنيين الصحيين (healthcare professionals) الموارد اللازمة لتطوير والتحقق من حلول مبتكرة لـ (machine learning) يمكن أن تنقذ الأرواح وتقلل العبء العالمي لهذا المرض المزمن.

---

**وصف البيانات (Data Description)**

الجدول التالي يقدّم وصفًا شاملاً لجميع المتغيرات (variables) في (dataset)، بما في ذلك أنواع البيانات (data types)، معانيها (meanings)، والقيم أو النطاقات المحتملة (possible values or ranges).

|العمود (Column)|النوع (Type)|الوصف (Description)|القيم / النطاق (Values/Range)|
|---|---|---|---|
|subject_code|Integer|معرف فريد للمريض (Unique patient identifier)|1–100000|
|years_old|Integer|عمر المريض بالسنوات (Age of patient in years)|18–90|
|sex|String|جنس المريض (Patient gender)|‘M’, ‘F’, ‘O’|
|ethnic_group|String|الخلفية العرقية (Ethnic background)|‘Group_A’, ‘Group_B’, ‘Group_C’, ‘Group_D’, ‘Group_E’|
|edu_status|String|أعلى مستوى تعليم مكتمل (Highest completed education)|‘Level_0’, ‘Level_1’, ‘Level_2’, ‘Level_3’|
|income_class|String|فئة الدخل (Income category)|‘L’, ‘M’, ‘H’|
|work_status|String|نوع العمل (Employment type)|‘Status_1’, ‘Status_2’, ‘Status_3’, ‘Status_4’|
|tobacco_use|String|سلوك التدخين (Smoking behavior)|‘N’, ‘F’, ‘C’|
|weekly_alcohol|Float|عدد المشروبات في الأسبوع (Drinks consumed per week)|0–30|
|weekly_exercise_min|Integer|النشاط البدني بالأسابيع بالدقائق (Physical activity weekly minutes)|0–600|
|nutrition_index|Integer|جودة النظام الغذائي (Diet quality, higher = healthier)|0–10|
|daily_sleep_hrs|Float|متوسط ساعات النوم اليومية (Average daily sleep hours)|3–12|
|daily_screen_hrs|Float|متوسط ساعات الشاشة اليومية (Average daily screen time hours)|0–12|
|family_diabetes|Integer|التاريخ العائلي للسكري (Family history of diabetes)|0 = No, 1 = Yes|
|has_hypertension|Integer|تاريخ ارتفاع ضغط الدم (Hypertension history)|0 = No, 1 = Yes|
|has_cardiovascular|Integer|تاريخ أمراض القلب والأوعية الدموية (Cardiovascular history)|0 = No, 1 = Yes|
|body_mass_idx|Float|مؤشر كتلة الجسم (Body Mass Index (kg/m²))|15–45|
|whr|Float|نسبة الخصر إلى الورك (Waist-to-hip ratio)|0.7–1.2|
|bp_systolic|Integer|ضغط الدم الانقباضي (Systolic blood pressure (mmHg))|90–180|
|bp_diastolic|Integer|ضغط الدم الانبساطي (Diastolic blood pressure (mmHg))|60–120|
|pulse_rate|Integer|معدل ضربات القلب أثناء الراحة (Resting heart rate (bpm))|50–120|
|total_chol|Float|الكوليسترول الكلي (Total cholesterol (mg/dL))|120–300|
|hdl_chol|Float|الكوليسترول الجيد (HDL cholesterol (mg/dL))|20–100|
|ldl_chol|Float|الكوليسترول الضار (LDL cholesterol (mg/dL))|50–200|
|trig_level|Float|الدهون الثلاثية (Triglycerides (mg/dL))|50–500|
|fasting_glucose|Float|الجلوكوز الصائم (Fasting glucose (mg/dL))|70–250|
|post_meal_glucose|Float|الجلوكوز بعد الوجبة (Post-meal glucose (mg/dL))|90–350|
|insulin_reading|Float|مستوى الإنسولين في الدم (Blood insulin level (µU/mL))|2–50|
|hemoglobin_a1c|Float|الهيموغلوبين A1c (HbA1c (%))|4–14|
|risk_index|Integer|درجة الخطر (Risk score, calculated, 0–100)|0–100|
|condition_level|String|مرحلة السكري (Stage of diabetes)|‘No Diabetes’, ‘Pre-Diabetes’, ‘Type 1’, ‘Type 2’, ‘Gestational’|
|has_condition|Integer|الهدف: تشخيص السكري (Target: Diabetes diagnosis)|0 = No, 1 = Yes|

---

إذا أحببت، أستطيع أن أصنع نسخة **مهيأة وجاهزة للطباعة** بالعربية مع إبقاء كل الكلمات التقنية بالإنجليزية، بحيث تكون مناسبة كمذكرة رسمية للامتحان.

هل تريد أن أفعل ذلك؟




---

### 1. **subject_code (Integer)**

- **ما هو:** معرف فريد لكل مريض (Unique identifier).
    
- **لماذا مهم:** لا يعطي أي معلومات للتنبؤ بالسكري، فقط لتمييز الصفوف (rows) في البيانات.
    
- **كيف تدرسه:**
    
    - لا تستخدمه كمتغير في النماذج (model) لأنه لا يحتوي على معنى.
        
    - يمكن استخدامه فقط للتأكد من عدم تكرار البيانات.
        

---

### 2. **years_old (Integer)**

- **ما هو:** عمر المريض بالسنوات (Age of patient in years).
    
- **لماذا مهم:** العمر عامل خطر أساسي للسكري، بعض الأعمار تكون أكثر عرضة.
    
- **كيف تدرسه:**
    
    - رسم توزيع الأعمار (histogram) لمعرفة الفئات العمرية الأكثر.
        
    - دراسة العلاقة مع السكري (has_condition) باستخدام متوسط العمر لكل حالة.
        
    - يمكن استخدامه مباشرة كنقطة بيانات في النماذج.
        

---

### 3. **sex (String)**

- **ما هو:** جنس المريض (Patient gender). القيم: ‘M’ ذكر، ‘F’ أنثى، ‘O’ آخر.
    
- **لماذا مهم:** بعض الأمراض والسكري تختلف باختلاف الجنس.
    
- **كيف تدرسه:**
    
    - احسب نسبة كل جنس في البيانات.
        
    - يمكن استخدامه كنقطة بيانات مصنفة (categorical variable) للنماذج.
        

---

### 4. **ethnic_group (String)**

- **ما هو:** الخلفية العرقية (Ethnic background) مثل ‘Group_A’ إلى ‘Group_E’.
    
- **لماذا مهم:** بعض المجموعات العرقية أكثر عرضة للسكري بسبب الجينات أو العادات الغذائية.
    
- **كيف تدرسه:**
    
    - دراسة نسبة السكري لكل مجموعة عرقية.
        
    - تحويلها إلى أرقام (encoding) إذا أردت استخدامها في النماذج.
        

---

### 5. **edu_status (String)**

- **ما هو:** أعلى مستوى تعليم أكمله الشخص (Highest completed education).
    
- **لماذا مهم:** التعليم يمكن أن يؤثر على الوعي الصحي والسلوكيات الغذائية.
    
- **كيف تدرسه:**
    
    - احسب عدد الأشخاص في كل مستوى تعليمي.
        
    - دراسة العلاقة بين مستوى التعليم وخطر السكري.
        

---

### 6. **income_class (String)**

- **ما هو:** فئة الدخل (Income category) مثل ‘L’ منخفض، ‘M’ متوسط، ‘H’ مرتفع.
    
- **لماذا مهم:** الدخل يمكن أن يؤثر على نوعية الغذاء والرعاية الصحية.
    
- **كيف تدرسه:**
    
    - تحليل السكري حسب الفئة المالية.
        
    - يمكن استخدامه في النماذج كنقطة بيانات مصنفة (categorical).
        

---

### 7. **work_status (String)**

- **ما هو:** نوع العمل (Employment type) مثل Status_1 إلى Status_4.
    
- **لماذا مهم:** بعض الوظائف أكثر نشاطًا أو أكثر توترًا مما قد يؤثر على صحة الشخص.
    
- **كيف تدرسه:**
    
    - دراسة العلاقة بين نوع العمل وخطر السكري.
        
    - تحويله إلى أرقام (encoding) إذا أردت استخدامه في النماذج.
        

---

### 8. **tobacco_use (String)**

- **ما هو:** سلوك التدخين (Smoking behavior): N = لا يدخن، F = سابقًا، C = يدخن حاليًا.
    
- **لماذا مهم:** التدخين يزيد من مخاطر أمراض القلب والسكري.
    
- **كيف تدرسه:**
    
    - احسب نسبة كل فئة وتأثيرها على السكري.
        
    - استخدامه كمتغير مصنف (categorical) في النماذج.
        

---

### 9. **weekly_alcohol (Float)**

- **ما هو:** عدد المشروبات الكحولية في الأسبوع (Drinks consumed per week).
    
- **لماذا مهم:** الإفراط في الكحول يؤثر على الأيض ويزيد من خطر السكري.
    
- **كيف تدرسه:**
    
    - رسم توزيع الاستهلاك الأسبوعي.
        
    - دراسة العلاقة بين كمية الكحول وخطر السكري.
        

---

### 10. **weekly_exercise_min (Integer)**

- **ما هو:** النشاط البدني الأسبوعي بالدقائق (Physical activity weekly minutes).
    
- **لماذا مهم:** قلة النشاط البدني تزيد من مخاطر السكري والسمنة.
    
- **كيف تدرسه:**
    
    - دراسة العلاقة بين النشاط البدني وخطر السكري.
        
    - يمكن تقسيمه إلى فئات: منخفض، متوسط، مرتفع.
        

### 11. **nutrition_index (Integer)**

- **ما هو:** مؤشر جودة النظام الغذائي (Diet quality)، كلما ارتفع الرقم كلما كان النظام الغذائي أفضل وصحي أكثر.
    
- **لماذا مهم:** النظام الغذائي السيء يزيد من خطر السكري والسمنة.
    
- **كيف تدرسه:**
    
    - تحليل توزيع (nutrition_index) في البيانات.
        
    - دراسة العلاقة بين جودة النظام الغذائي وخطر السكري.
        

---

### 12. **daily_sleep_hrs (Float)**

- **ما هو:** متوسط عدد ساعات النوم اليومية (Average daily sleep hours).
    
- **لماذا مهم:** النوم غير الكافي أو المفرط مرتبط بمخاطر السكري.
    
- **كيف تدرسه:**
    
    - رسم توزيع ساعات النوم.
        
    - دراسة العلاقة بين النوم وخطر السكري.
        

---

### 13. **daily_screen_hrs (Float)**

- **ما هو:** متوسط ساعات الشاشة اليومية (Average daily screen time hours).
    
- **لماذا مهم:** وقت الشاشة الطويل غالبًا مرتبط بقلة النشاط البدني وزيادة الوزن.
    
- **كيف تدرسه:**
    
    - دراسة العلاقة بين ساعات الشاشة وخطر السكري.
        

---

### 14. **family_diabetes (Integer)**

- **ما هو:** وجود تاريخ عائلي للسكري (Family history of diabetes) 0 = لا، 1 = نعم.
    
- **لماذا مهم:** التاريخ العائلي عامل خطر قوي جدًا.
    
- **كيف تدرسه:**
    
    - مقارنة نسبة السكري بين من لديهم تاريخ عائلي ومن لا.
        
    - استخدامه كنقطة بيانات مهمة في النماذج.
        

---

### 15. **has_hypertension (Integer)**

- **ما هو:** وجود تاريخ لارتفاع ضغط الدم (Hypertension history) 0 = لا، 1 = نعم.
    
- **لماذا مهم:** ارتفاع ضغط الدم مرتبط بالسكري وأمراض القلب.
    
- **كيف تدرسه:**
    
    - دراسة العلاقة بين وجود ارتفاع ضغط الدم وخطر السكري.
        

---

### 16. **has_cardiovascular (Integer)**

- **ما هو:** وجود تاريخ لأمراض القلب والأوعية الدموية (Cardiovascular history) 0 = لا، 1 = نعم.
    
- **لماذا مهم:** الأشخاص المصابون بأمراض القلب أكثر عرضة للسكري.
    
- **كيف تدرسه:**
    
    - دراسة العلاقة مع السكري.
        

---

### 17. **body_mass_idx (Float)**

- **ما هو:** مؤشر كتلة الجسم (Body Mass Index, BMI) بالكيلوغرام/متر².
    
- **لماذا مهم:** السمنة عامل خطر رئيسي للسكري.
    
- **كيف تدرسه:**
    
    - رسم توزيع (histogram) للـ BMI.
        
    - تقسيمه لفئات: نحيف، طبيعي، زائد وزن، سمنة.
        
    - دراسة العلاقة مع السكري.
        

---

### 18. **whr (Float)**

- **ما هو:** نسبة الخصر إلى الورك (Waist-to-hip ratio).
    
- **لماذا مهم:** تجمع الدهون حول البطن يزيد من خطر السكري أكثر من السمنة العامة.
    
- **كيف تدرسه:**
    
    - رسم توزيع النسبة.
        
    - دراسة العلاقة مع السكري.
        

---

### 19. **bp_systolic (Integer)**

- **ما هو:** ضغط الدم الانقباضي (Systolic blood pressure) بالملم زئبقي (mmHg).
    
- **لماذا مهم:** ضغط الدم المرتفع مرتبط بخطر السكري وأمراض القلب.
    
- **كيف تدرسه:**
    
    - دراسة متوسطات الضغط الانقباضي بين المرضى وغير المرضى.
        

---

### 20. **bp_diastolic (Integer)**

- **ما هو:** ضغط الدم الانبساطي (Diastolic blood pressure) بالملم زئبقي.
    
- **لماذا مهم:** نفس أهمية الضغط الانقباضي.
    
- **كيف تدرسه:**
    
    - تحليل العلاقة مع السكري.
        

---

### 21. **pulse_rate (Integer)**

- **ما هو:** معدل ضربات القلب أثناء الراحة (Resting heart rate, bpm).
    
- **لماذا مهم:** ارتفاع معدل ضربات القلب قد يدل على مشاكل قلبية أو ضعف اللياقة البدنية.
    
- **كيف تدرسه:**
    
    - دراسة متوسط معدل ضربات القلب حسب وجود السكري.
        

---

### 22. **total_chol (Float)**

- **ما هو:** الكوليسترول الكلي (Total cholesterol, mg/dL).
    
- **لماذا مهم:** ارتفاع الكوليسترول مرتبط بالسمنة وأمراض القلب والسكري.
    
- **كيف تدرسه:**
    
    - دراسة العلاقة بين الكوليسترول وخطر السكري.
        

---

### 23. **hdl_chol (Float)**

- **ما هو:** الكوليسترول الجيد (HDL cholesterol, mg/dL).
    
- **لماذا مهم:** انخفاض HDL يزيد من خطر السكري وأمراض القلب.
    
- **كيف تدرسه:**
    
    - دراسة العلاقة مع السكري.
        

---

### 24. **ldl_chol (Float)**

- **ما هو:** الكوليسترول الضار (LDL cholesterol, mg/dL).
    
- **لماذا مهم:** ارتفاع LDL عامل خطر للسكري وأمراض القلب.
    
- **كيف تدرسه:**
    
    - تحليل العلاقة مع السكري.
        

---

### 25. **trig_level (Float)**

- **ما هو:** مستوى الدهون الثلاثية (Triglycerides, mg/dL).
    
- **لماذا مهم:** ارتفاعها مرتبط بالسمنة والسكري.
    
- **كيف تدرسه:**
    
    - دراسة العلاقة بين الدهون الثلاثية وخطر السكري.
        

---

### 26. **fasting_glucose (Float)**

- **ما هو:** الجلوكوز الصائم (Fasting glucose, mg/dL).
    
- **لماذا مهم:** ارتفاعه مؤشر مباشر على السكري أو ما قبل السكري.
    
- **كيف تدرسه:**
    
    - تقسيمه لفئات: طبيعي، ما قبل السكري، سكري.
        
    - مهم جدًا كنقطة بيانات رئيسية للتنبؤ.
        

---

### 27. **post_meal_glucose (Float)**

- **ما هو:** الجلوكوز بعد الوجبة (Post-meal glucose, mg/dL).
    
- **لماذا مهم:** ارتفاعه بعد الأكل يظهر خلل أيضي مبكر.
    
- **كيف تدرسه:**
    
    - دراسة العلاقة مع السكري.
        

---

### 28. **insulin_reading (Float)**

- **ما هو:** مستوى الإنسولين في الدم (Blood insulin level, µU/mL).
    
- **لماذا مهم:** يقيس قدرة الجسم على تنظيم السكر.
    
- **كيف تدرسه:**
    
    - دراسة العلاقة بين مستوى الإنسولين وخطر السكري.
        

---

### 29. **hemoglobin_a1c (Float)**

- **ما هو:** الهيموغلوبين A1c (%) (HbA1c).
    
- **لماذا مهم:** يعطي متوسط نسبة السكر في الدم خلال 2–3 أشهر، مؤشر قوي على السكري.
    
- **كيف تدرسه:**
    
    - تقسيمه لفئات: طبيعي، ما قبل السكري، سكري.
        
    - مهم جدًا للنماذج التنبؤية.
        

---

### 30. **risk_index (Integer)**

- **ما هو:** درجة الخطر (Risk score, 0–100).
    
- **لماذا مهم:** مؤشر مركب يعكس خطر السكري اعتمادًا على عدة عوامل.
    
- **كيف تدرسه:**
    
    - يمكن استخدامه مباشرة كنقطة بيانات للتنبؤ أو لتصنيف السكان.
        

---

### 31. **condition_level (String)**

- **ما هو:** مرحلة السكري (Stage of diabetes):
    
    - No Diabetes
        
    - Pre-Diabetes
        
    - Type 1
        
    - Type 2
        
    - Gestational
        
- **لماذا مهم:** يوضح حالة المريض الحالية.
    
- **كيف تدرسه:**
    
    - دراسة توزيع المراحل بين السكان.
        
    - تحويلها إلى أرقام (encoding) إذا أردت استخدامها في النماذج.
        

---

### 32. **has_condition (Integer)**

- **ما هو:** الهدف النهائي (Target): هل المريض مصاب بالسكري؟ 0 = لا، 1 = نعم.
    
- **لماذا مهم:** هذا هو ما نحاول التنبؤ به بالنماذج (dependent variable).
    
- **كيف تدرسه:**
    
    - هذا المتغير يستخدم كنقطة خروج (target) في كل التحليلات والنماذج.
        



Great 👍 I’ll reorganize the variables **cleanly and professionally with English section titles** (you can copy this directly into your report).

---

# **1. Demographic Variables**

These variables describe the basic characteristics of the individuals.

- **subject_code** – Unique patient identifier
    
- **years_old** – Age of the patient
    
- **sex** – Gender (M, F, O)
    
- **ethnic_group** – Ethnic background
    
- **edu_status** – Education level
    
- **income_class** – Income category
    
- **work_status** – Employment status
    

---

# **2. Lifestyle and Behavioral Factors**

These variables capture daily habits that influence diabetes risk.

- **tobacco_use** – Smoking behavior
    
- **weekly_alcohol** – Weekly alcohol consumption
    
- **weekly_exercise_min** – Physical activity per week
    
- **nutrition_index** – Diet quality score
    
- **daily_sleep_hrs** – Average daily sleep hours
    
- **daily_screen_hrs** – Daily screen time
    

---

# **3. Family and Medical History**

These variables describe inherited and previous health conditions.

- **family_diabetes** – Family history of diabetes
    
- **has_hypertension** – History of hypertension
    
- **has_cardiovascular** – History of cardiovascular disease
    

---

# **4. Anthropometric and Physical Measurements**

These variables describe body composition and vital signs.

- **body_mass_idx (BMI)** – Body Mass Index
    
- **whr** – Waist-to-hip ratio
    
- **bp_systolic** – Systolic blood pressure
    
- **bp_diastolic** – Diastolic blood pressure
    
- **pulse_rate** – Resting heart rate
    

---

# **5. Lipid Profile Variables**

These variables measure blood fat levels.

- **total_chol** – Total cholesterol
    
- **hdl_chol** – HDL cholesterol
    
- **ldl_chol** – LDL cholesterol
    
- **trig_level** – Triglycerides
    

---

# **6. Glycemic and Hormonal Variables**

These variables are directly related to diabetes diagnosis.

- **fasting_glucose** – Fasting blood glucose
    
- **post_meal_glucose** – Post-meal glucose level
    
- **insulin_reading** – Blood insulin level
    
- **hemoglobin_a1c** – HbA1c percentage
    

---

# **7. Risk and Clinical Outcome Variables**

These variables represent risk assessment and disease status.

- **risk_index** – Calculated diabetes risk score
    
- **condition_level** – Diabetes stage (No Diabetes, Pre-Diabetes, Type 1, Type 2, Gestational)
    
- **has_condition** – Target variable (0 = No Diabetes, 1 = Diabetes)
    

---

# ✅ Tip for Your Report (Professor Impression Trick)

After this categorization, you can add one sentence like:

> _The variables were grouped into demographic, lifestyle, medical history, anthropometric, lipid, glycemic, and outcome categories to facilitate structured exploratory data analysis and modeling._

This sounds **very scientific and PhD-level**.

---

```
Demographic
Lifestyle
Medical History
Anthropometric
Lipid Profile
Glycemic Variables
          ↓
   Feature Engineering
          ↓
   Machine Learning Models
          ↓
     Diabetes Prediction
```