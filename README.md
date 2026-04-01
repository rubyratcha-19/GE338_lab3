# GE338_lab3
README คำตอบภารกิจ
1. ผลการเพิ่ม Training Samples

จากการทดลองพบว่า

Accuracy ของ 3000 samples = 0.7387
Accuracy ของ 6000 samples = 0.7671
Improvement ประมาณ 0.0284 หรือ 2.84%

อธิบายผล
การเพิ่มจำนวน Training Samples ทำให้โมเดลมีข้อมูลตัวอย่างหลากหลายขึ้น ซึ่งช่วยให้เรียนรู้รูปแบบของแต่ละ class ได้ดีขึ้น และลด bias ในการจำแนก class ที่มีความซับซ้อน เช่น Built-up และ Agriculture

ผล improvement ไม่ได้เพิ่มมากเกินไป เนื่องจากบาง class อาจมีข้อมูลไม่เพียงพอ หรือ spatial correlation ของข้อมูลสูง ทำให้การเรียนรู้มีขีดจำกัด

2. Spatial Autocorrelation ใน Training Data
   
Spatial autocorrelation คือปรากฏการณ์ที่พิกัดใกล้กันมักมี class เหมือนกัน ทำให้ข้อมูลไม่เป็นอิสระต่อกัน

ผลต่อความแม่นยำ
การแบ่ง Train/Test แบบสุ่ม อาจทำให้ pixel ใกล้กันอยู่ในทั้ง Train และ Test ทำให้ Accuracy ถูกประเมินสูงเกินจริง
หากใช้ Spatial Cross-validation โมเดลต้องทำนายพื้นที่ที่อยู่ไกลจาก Training ทำให้ Accuracy มักลดลง แต่สะท้อนความสามารถจริงของโมเดลมากกว่า

3. Class ที่โมเดลทำได้แย่ที่สุด
จาก F1-score พบว่า Class ที่ต่ำสุดคือ Vegetation (ประมาณ 0.21)

Spectral Signature ของ Vegetation คล้ายกับ Class ใกล้เคียง เช่น Agriculture ทำให้โมเดลสับสนง่าย
จำนวนตัวอย่างน้อย และ Spatial heterogeneity สูง

วิธีแก้
เพิ่มจำนวน Training Samples สำหรับ Vegetation
ใช้ Feature เพิ่มเติม เช่น NDVI, NDWI, NDBI
ทดลองอัลกอริทึมอื่น

4. ทำซ้ำ Lab สำหรับพื้นที่อื่น

ส่วนตัวมองว่าสามารถใช้ได้กับจังหวัดสุพรรณเพราะมีกิจกรรมทางการเกษตรที่หลากหลายพื้นที่ให้เลือกเยอะ มีระบบสาธารณูปโภค

สิ่งที่ต้องเปลี่ยน
AOI (Area of Interest) ปรับฟิลเตอร์ GAUL หรือพิกัดใหม่
Training Samples สร้าง Polygon ใหม่ หรือใช้ reference dataset ของพื้นที่นั้น
Parameter ของ Cloud Filtering และ Image Collection Dates ปรับตามปีและฤดูกาลของพื้นที่

สิ่งที่สามารถใช้ซ้ำได้
Workflow ของ Sentinel-2 preprocessing และ indices เช่น NDVI, NDWI, NDBI
การ train Random Forest หรือ K-Means
Metrics การประเมิน Accuracy, Kappa, F1-score
การสร้าง Map Visualization และ Legend

## 1. วิธีการเลือกพื้นที่ศึกษา
พื้นที่ศึกษาเลือกเป็นอำเภอบางเลน จังหวัดนครปฐม เนื่องจากมีการใช้ประโยชน์ที่ดินหลากหลาย ทั้งพื้นที่น้ำ พื้นที่เกษตร พื้นที่ป่าและพืชพรรณ รวมถึงพื้นที่ชุมชน ทำให้เหมาะสมต่อการทดสอบโมเดลจำแนกประเภทที่ดิน นอกจากนี้ ข้อมูล Sentinel-2 ของปี 2023 มี cloud cover ต่ำและครอบคลุมพื้นที่ทั้งหมด ขนาดพื้นที่ไม่ใหญ่มากช่วยให้การประมวลผลและการสุ่มตัวอย่าง pixels สำหรับ training และ testing เป็นไปอย่างเหมาะสม

## 2. วิธีการและเหตุผลในการวิเคราะห์
ในการสร้างโมเดลจำแนกประเภทที่ดิน ใช้ภาพ Sentinel-2 Surface Reflectance เป็นฐานข้อมูล พร้อมคำนวณดัชนีเช่น NDVI, NDWI และ NDBI เพื่อเพิ่มข้อมูลเชิงสารสนเทศให้กับโมเดล สำหรับ label ใช้ Dynamic World dataset และทำการ reclass เป็น 5 classes ได้แก่ Water, Agriculture, Vegetation, Built-up และ Bare soil

สุ่มตัวอย่างข้อมูลจำนวน 3000 pixels และแบ่งเป็นชุดฝึกสอนและทดสอบในอัตรา 80 ต่อ 20 จากนั้นทำการ train Random Forest สองชุด ได้แก่ Band-only และ Band plus indices ประเมินผลโดยใช้ Accuracy, Kappa, F1-score และ Feature Importance เพื่อวิเคราะห์ความสำคัญของตัวแปรแต่ละ band และ index นอกจากนี้ยังทำการจำแนกแบบไม่ใช้ supervised ด้วย K-Means เพื่อเปรียบเทียบผลลัพธ์

## 3. ข้อจำกัดที่พบ
พบว่า Spatial autocorrelation ในข้อมูล training ส่งผลต่อความแม่นยำที่รายงาน เนื่องจาก pixels ใกล้กันมักมีค่า spectral คล้ายกัน ทำให้โมเดลอาจ overfit ต่อบริเวณบางส่วน และ F1-score สำหรับบาง class เช่น Vegetation และ Bare soil ต่ำกว่าชั้นอื่น ๆ นอกจากนี้ จำนวนตัวอย่าง training เพียง 3000 pixels อาจยังไม่เพียงพอสำหรับ class ที่มีการกระจายไม่สม่ำเสมอ

## 4. สิ่งที่ค้นพบ
- เมื่อเพิ่มจำนวน training samples เป็น 6000 pixels ความแม่นยำ Accuracy เพิ่มจาก 0.739 เป็น 0.767 ซึ่งสอดคล้องกับความคาดหมายว่าการมีข้อมูลฝึกสอนมากขึ้นช่วยให้โมเดลเรียนรู้ได้ดีขึ้น แต่การเพิ่มมากเกินไปอาจเพิ่มเวลาในการประมวลผลโดยไม่เพิ่มประสิทธิภาพมากนัก  
- Random Forest แสดงความสำคัญของ band และ index ที่หลากหลาย เช่น B12 และ B2 มีค่า importance สูง ซึ่งสอดคล้องกับความเป็นจริงทางกายภาพในการแยกพื้นที่เกษตรและพื้นที่ชุมชน  
- Class ที่โมเดลทำได้แย่ที่สุดคือ Vegetation และ Bare soil สามารถแก้ไขโดยเพิ่มจำนวน training samples ของ class เหล่านี้ หรือใช้ spatial cross-validation เพื่อลด bias จาก spatial autocorrelation  
- หากต้องทำซ้ำสำหรับพื้นที่อื่น จำเป็นต้องปรับ AOI, ช่วงวันที่ และอาจปรับจำนวน samples ตามความหลากหลายของที่ดิน ส่วน band และ index สามารถนำกลับมาใช้ซ้ำได้ โดยต้องคำนึงถึงสภาพภูมิประเทศและชนิดพืชที่แตกต่างกัน  

## 5. ข้อเสนอแนะ
- การใช้ Spatial Cross-validation แทนการสุ่มแบบทั่วพื้นที่ อาจช่วยลด bias จาก spatial autocorrelation  
- การเพิ่ม training samples สำหรับ class ที่มีการกระจายไม่สม่ำเสมอ จะช่วยปรับปรุง F1-score และ Kappa  
- การใช้ indices เช่น NDVI, NDWI, และ NDBI ช่วยเพิ่มข้อมูลเชิงสารสนเทศ แม้จะไม่เพิ่ม Accuracy อย่างมาก แต่ช่วยให้โมเดลจำแนก class ยากได้ดีขึ้น  
