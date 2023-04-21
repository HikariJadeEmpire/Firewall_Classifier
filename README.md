# Firewall_Classifier
Short Description :
> เกี่ยวกับการ **แยกแยะหมวดหมู่ ( Classification )** ของ Firewall action โดยอาศัย Machine Learning algorithm

DATA : <a href="https://github.com/HikariJadeEmpire/Firewall_Classifier/blob/main/log2.csv">CSV</a>
<br>
Source : <a href="https://github.com/HikariJadeEmpire/Firewall_Classifier/blob/main/Paper.pdf">PAPER</a>

<h3>DESCRIBTION</h3>

**อธิบาย <a href="https://github.com/HikariJadeEmpire/Firewall_Classifier/blob/main/Paper.pdf">PAPER</a>** :

Firewall = เปรียบเสมือนเป็นประตูทางผ่านเข้า-ออกของคอมพิวเตอร์ ซึ่งจะมีการตรวจสอบ File ต่างๆ ก่อนที่จะมีการอนุญาตให้ผ่านประตูไปได้

> จุดประสงค์ของ <a href="https://github.com/HikariJadeEmpire/Firewall_Classifier/blob/main/Paper.pdf">PAPER</a> ดังกล่าว มีเพื่อการศึกษาพฤติกรรมของ Firewall จาก Firewall log file ของ Firat University โดยมีเป้าหมายเพื่อการลดกฏเกณฑ์ภายใน Firewall หรืออธิบายง่ายๆก็คือการทำให้ Firewall นั้นฉลาดขึ้น และสามารถตรวจสอบไฟล์ต่างๆได้โดยที่ไม่ต้องอาศัย Active firewall rules ( กฏเกณฑ์ภายใน Firewall ) เพียงอย่างเดียว <br>
>
> ซึ่งวิธีการก็คือการนำ Machine Learning Model ที่มีชื่อว่า SVM ( Support Vector Machines ) เข้าไปประยุกต์ใช้ในการ **แยกแยะหมวดหมู่ ( Classification )** ของ file ประเภทต่างๆ แล้วนำมาประเมินความแม่นยำของ Model โดยภาพรวมด้วย F1 Score
<br>
จุดประสงค์ของ Project นี้ คือการพัฒนาต่อจาก <a href="https://github.com/HikariJadeEmpire/Firewall_Classifier/blob/main/Paper.pdf">PAPER</a> ที่นำมาศึกษา โดยที่จะมีการปรับเปลี่ยนวิธีการและการเลือกใช้ Model ที่แตกต่างออกไป เพื่อให้ได้ผลลัพธ์ ( F1 score ) ที่ดียิ่งขึ้น
<br>

# PROCESS
CODE : <a href="https://github.com/HikariJadeEmpire/Firewall_Classifier/blob/main/ML_Firewall.ipynb">Code</a>

**STEP 1** : เริ่มต้นเราก็จะทำการเรียนรู้ DATA File โดยการเข้าใจความหมายของแต่ละ Column และทำการ Clean หรือ การ Preprocessing ก่อนที่จะนำไปทดสอบกับ Machine Learning Model ประเภทต่างๆ


