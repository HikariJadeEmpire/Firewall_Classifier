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
> วิธีการก็คือ นำ Machine Learning Model ที่มีชื่อว่า SVM ( Support Vector Machines ) เข้าไปประยุกต์ใช้ในการ **แยกแยะหมวดหมู่ ( Classification )** ของการส่ง file ประเภทต่างๆ ซึ่งก่อนจะเอาไปใช้กับ Firewall จะต้องมีการประเมินความแม่นยำของ Model โดยภาพรวมด้วย F1 Score เพื่อพิจารณาว่าควรนำไปใช้กับ Firewall หรือไม่ ?
<br>
จุดประสงค์ของ Project นี้ คือการพัฒนาต่อจาก <a href="https://github.com/HikariJadeEmpire/Firewall_Classifier/blob/main/Paper.pdf">PAPER</a> ที่นำมาศึกษา โดยที่จะมีการปรับเปลี่ยนวิธีการและการเลือกใช้ Model ที่แตกต่างออกไป เพื่อให้ได้ผลลัพธ์ ( F1 score ) ที่ดียิ่งขึ้น
<br>

# PROCESS
CODE : <a href="https://github.com/HikariJadeEmpire/Firewall_Classifier/blob/main/ML_Firewall.ipynb">Code</a>

**STEP 1** : เริ่มต้นเราก็จะทำการเรียนรู้ DATA File โดยการเข้าใจความหมายของแต่ละ Column

![ML000](https://user-images.githubusercontent.com/118663358/233534910-0ae8603d-2de2-41cf-902c-4ce5308f12ea.JPG)

Source Port : ที่มาของ file <br>
Destination Port : จุดหมายปลายทางที่ต้องการส่ง <br>
NAT Source Port : จุดเปลี่ยน Port 
> ยกตัวอย่างเช่น เมื่อเราส่งของ เราจะต้องระบุที่อยู่ของเรา และที่อยู่ปลายทาง ซึ่งเวลาส่งไปที่คนกลาง คนกลางก็จะทำการปกปิดข้อมูลผู้ส่ง แล้วเปลี่ยนข้อมูลผู้ส่งเป็นที่อยู่ของคนกลางแทน ซึ่ง NAT Port มักจะใช้เมื่อเราส่งข้อมูลผ่าน Internet <br>

NAT Destination Port : จุดหมายปลายทางที่ต้องการส่ง ( Destination Port ) <br>
Bytes : ขนาดของ File ที่ส่ง ซึ่งมีหน่วยเป็น Bytes <br>
Packets : จำนวน File ทั้งหมดที่ส่ง <br>
pkts_sent : จำนวน File ที่ส่งไปจริงๆ <br>
pkts_received : จำนวน File ที่ได้รับ <br>

Action : คือผลลัพธ์การตัดสินใจของ Firewall ที่ผ่านการตรวจสอบ Active firewall rules ( กฏเกณฑ์ภายใน Firewall ) แล้ว ซึ่งจะมีผลลัพธ์ออกมาทั้งหมด 4 แบบ ได้แก่
> 1. Allow
> 2. Deny
> 3. Drop
> 4. Reset-both
<br>

**จุดประสงค์ของเรา** คือการให้ Machine Learning algorithm ทำนาย Action สุดท้ายที่ Firewall เป็นคนตัดสินใจ จากการรวบรวมข้อมูลในแต่ละ Column โดยที่ไม่ต้องผ่าน Active firewall rules ( กฏเกณฑ์ภายใน Firewall ) <br>

และเราได้มีการตัดสินใจว่าจะใช้ Column ทุกอันเข้ามาเป็นข้อมูลในการ Learn

ดังนั้น เมื่อมาถึงตรงนี้ เราก็จะทำการเปลี่ยน column port ทั้งหมด ให้เป็น String แล้วแยก Column ผลลัพธ์ออกมา <br>
ซึ่งจะเขียน Code ได้ลักษณะนี้ : 
    
```ruby

# Prepare X and Y

x = df.drop( columns = ['Action'] )
Y = df.loc[ :, [ "Action" ] ]

```

จากนั้น เราก็ทำการ **Encoding** Y ตาม Code ด้านล่าง :

```ruby

# Binarize the output

from sklearn import preprocessing

y = preprocessing.label_binarize(Y, classes=['allow', 'deny', 'drop', 'reset-both'])
n_classes = y.shape[1]

```

อธิบายง่ายๆก็คือ การเปลี่ยนจาก 'allow', 'deny', 'drop', 'reset-both' ที่เรียงตัวอยู่ใน Column เดียว คือ 'Action' ให้กลายเป็น 4 Column โดยแบ่ง Column เป็น 'allow', 'deny', 'drop', 'reset-both' ตามลำดับ <br> 
โดยที่ค่า value ของแต่ละ Column จะมีเพียง 1 กับ 0 ซึ่ง 1 หมายความว่า 'ใช่' และ 0 คือ 'ไม่ใช่'
<br>

วิธีการ **Encoding** จะช่วยให้ Machine Learning ทำงานง่ายขึ้น

#

**STEP 2** : จากนั้น เราก็จะนำ x และ y ไป train ผ่าน Model ต่างๆ โดยเราจะทำการประเมินเบื้องต้นว่า Model ไหนที่เหมาะจะนำไปใช้มากที่สุด

ซึ่งวิธีการดังกล่าวนั้น เราจะใช้วิธีการ Cross Validation ในการประเมิน ตัวอย่างเช่น :

```ruby

# Split By Cross Validation Method (Before train & test Split)
# try on Decision Tree

from sklearn import tree
from sklearn.model_selection import KFold, train_test_split, cross_validate, cross_val_score

clf = tree.DecisionTreeClassifier(criterion = 'gini', random_state = 42)

k_folds = KFold(n_splits = 5)

scores = cross_val_score(clf, x, y, cv = k_folds)

print("Cross Validation Scores : ", scores)
print("Average CV Score : ", scores.mean())
print("Number of CV Scores used in Average : ", len(scores))

```
Output :
> Cross Validation Scores :  [0.99740597 0.99740597 0.99786357 0.99694796 0.99771097] <br>
> Average CV Score :  0.9974668882659461 <br>
> Number of CV Scores used in Average :  5 <br>

และ

```ruby

# try on KNN

from sklearn.neighbors import KNeighborsClassifier

for i in range(1,6) :
    knn = KNeighborsClassifier( n_neighbors = i )

    scores_1 = cross_val_score(knn, x, y, cv = k_folds)

    print(f"KNN CV TEST at n_neighbors : {i}")
    print("Cross Validation Scores : ", scores_1)
    print("Average CV Score : ", scores_1.mean())
    print("Number of CV Scores used in Average : ", len(scores_1) ,'\n')

```
the Best Output :
> KNN CV TEST at n_neighbors : 3 <br>
> Cross Validation Scores :  [0.99458305 0.99389639 0.99298031 0.99267511 0.99221731] <br>
> Average CV Score :  0.9932704337030602 <br>
> Number of CV Scores used in Average :  5 <br>
<br>

ซึ่งผลลัพธ์ที่ได้ จะเป็น Accuracy score จากการ Train model เบื้องต้น โดยเราจะประเมินเพื่อเลือกใช้ Model ที่มีคะแนน CV score โดยเฉลี่ยสูงที่สุด <br>

จากการทดสอบดังกล่าว <br>
เราตัดสินใจเลือก Machine Learning Model ที่มีชื่อว่า ***Decision Tree***

#

**STEP 3** : Test

เริ่มด้วยการแบ่ง Train & Test

```ruby

# Train & Test Split

X_train, X_test, y_train, y_test = train_test_split( x, y, test_size = 0.20, random_state = 42 )

```
แล้วนำไปผ่านกระบวนการ Decision Tree ตามที่ได้เคยทำกับวิธีการ Cross Validation <br>

ซึ่งสำหรับ Decision Tree การพิจารณาเพิ่มเติมเกี่ยวกับ Depth หรือชั้นความลึก ของต้นไม้ เพื่อหาความแม่นยำของ Model ที่ดีที่สุด ก็เป็นหนึ่งในวิธีที่เราทำถัดมาหลังจากที่ได้ Model <br>
ซึ่งสามารถ Plot กราฟออกมาได้ ดังนี้ : 

![output](https://user-images.githubusercontent.com/118663358/233549517-d2297835-74c4-47b5-bf0b-a42c9f2de392.png)


จากนั้นเราก็ทำการ Predict ผลลัพธ์

```ruby

# Predict y

classifier = tree.DecisionTreeClassifier( criterion = 'gini', max_depth = 8 )
classifier.fit(X_train, y_train)
y_pred = classifier.predict(X_test)

print(f" F1 Score : {f1_score(y_test, y_pred, average = 'macro')} ")

```
Output :

> F1 Score : 0.9762997369548366

ก็จะได้คะแนนสุดท้ายออกมา ที่ราวๆ 97.63 คะแนน

ซึ่งสามารถ Plot แผนภาพต้นไม้ออกมาได้ดังนี้ : 

![output2](https://user-images.githubusercontent.com/118663358/233551655-7a6c2796-3588-4b96-b8d3-387ceabeb8a9.png)


จากนั้นเราก็สามารถ Plot ออกมาเป็น ROC Curve เพื่อดูคะแนนโดยรวมได้ ก็จะได้ออกมาดังภาพด้านล่าง : 

![output3](https://user-images.githubusercontent.com/118663358/233550639-76e0cd53-04c5-4e76-858e-fe50b8b0d3ef.png)

**NOTE**
- class 0 : 'allow'
- class 1 : 'deny'
- class 2 : 'drop'
- class 3 : 'reset-both'
<br>

# CONCLUSION


โดยสรุปแล้ว จาก <a href="https://github.com/HikariJadeEmpire/Firewall_Classifier/blob/main/Paper.pdf">PAPER</a> เราสามารถทำคะแนน ( F1 score ) ได้มากกว่า <br>
จากการเปลี่ยน Model
ซึ่งก็คือ **Decision Tree** โดยคะแนนจะอยู่ที่ราวๆ 97.63 คะแนน

สามารถดู CODE แบบละเอียดได้ที่นี่ : <a href="https://github.com/HikariJadeEmpire/Firewall_Classifier/blob/main/ML_Firewall.ipynb">Code</a>
