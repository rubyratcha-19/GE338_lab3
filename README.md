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
