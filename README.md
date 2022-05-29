# 🎛️ Web_Genshin_Builder
Repo ini akan membuat web lokal Genshin Builder, yaitu sebuah web yang dapat menyimpan dan menampilkan Build untuk karakter dari game Genshin Impact
Build pada web ini akan memuat karakter, senjata, serta 5 artifak saja.

## 📄 External Resource
FrontEnd pada repo ini menggunakan bantuan <a href="https://reactjs.org/" target="_blank">React.js</a> untuk membuat website yang dinamis, rapih dan bagus secara visual.

BackEnd pada repo ini menggunakan bantuan <a href="https://nodejs.org/en/" target="_blank">Node.js</a> untuk membuat API ke PSQL database.

## 📦 How to install
1. Download dan jalankan pg_restore untuk folder ```genshin_builder_dump.sql``` ke database PSQL yang disiapkan
2. Sesuaikan objek ```Client``` pada ```index.js``` ke database PSQL yang disiapkan sebelumnya
3. Lakukan ```npm install``` sesuai dengan library yang dibutuhkan (Hint: coba lakukan ```run``` dan jika terdapat error, install module yang tidak ditemukan)
4. Run index.js pada folder backend, dan lakukan langkah 3-4 pada folder frontend (gunakan ```npm start```)

## 🚀 How to use
1. Landing page, bisa melihat seluruh build yang ada pada page ini, dan melakukan login/register
2. silahkan coba register, pastikan username tidak sama seperti akun yang sudah ada sebelumnya
3. jika sudah bisa, silahkan login untuk melihat menu **PersonalBuild**
4. jika sudah login, **Build** pada landing page bisa di copy ke **PersonalBuild**
5. **PersonalBuild Page** akan memuat seluruh **Build** yang dimiliki akun tersebut
6. di page ini kita juga dapat menghapus **Build** yang ada dan membuat **Build** baru, serta melakukan pembaharuan pada **Build**

## 📁 Other Information for Usage
<details>
  <summary>🔁Route Table</summary>

  Route | Description
  --- | ---
  / | Landing page or home page
  /login | Login page, if logged then go to Landing page
  /register | Register page, goes to login if succeed
  /addbuild | Create new Build

</details>
<details>
  <summary>🔗UML and ERD Graph</summary>
  
  Berikut adalah diagram UML dan ERD-nya:
  ![Alt text](https://github.com/GreyWard/Web_Genshin_Builder/blob/main/Genshin_Builder-UML%20(1).png?raw=true)
  ![Alt text](https://github.com/GreyWard/Web_Genshin_Builder/blob/main/Genshin_Builder-ERD%20(1).png?raw=true)
  
</details>
<details>
  <summary>📸Screenshots</summary>
  
  ![](https://github.com/GreyWard/Web_Genshin_Builder/blob/main/Screenshot%202022-05-29%20215624.png?raw=true)
  ![](https://github.com/GreyWard/Web_Genshin_Builder/blob/main/Screenshot%202022-05-29%20215659.png?raw=true)
  
</details>

## 👨‍💻 Detailed Info for API

Berikut adalah penjelasan tiap API berdasarkan tabel database yang akan diakses oleh API

<details>
  <summary>🔨Build</summary>
  
  Terdapat beberapa operasi yang dilakukan pada tabel *Build*, operasi dibawah untuk landing page yaitu mengambil semua build yang ada pada database, melalui tabel *PersonalBuild* untuk melakukan grouping dan menghilangkan *Build* yang sudah tidak dipakai lagi.
  
```javascript
  app.get('/',(req,res)=>{
    db.query(`SELECT p."buildID" FROM public."PersonalBuild" as p INNER JOIN public."Build" as b ON p."buildID"=b."buildID" GROUP BY p."buildID";` ,(err,results)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(results.rows)
    })
})
  ```

  
  API dibawah ini berfungsi untuk mengambil *buildID* dengan parameter build yang ada, dan mengambil *Build* dari buildID yang diberikan, keduanya digunakan dalam pembuatan *Build* baru (memastikan tidak ada data redundant pada tabel *Build*), membuka informasi suatu *Build*, serta operasi **Copy Build**,**Delete Build**, dan **Update Build**.
  
  
```javascript
// Mengambil buildID yang memiliki atribut Build yang spesifik
app.get('/getbuildID',(req,res)=>{
    db.query(`SELECT buildID FROM public."Build" WHERE char_name ='${req.body.char}' && weapon = '${req.body.weapon}' && flower = '${req.body.flower}' && sands = '${req.body.sands}' && plume = '${req.body.plume}' && goblet = '${req.body.goblet}' && circlet = '${req.body.circlet}';`,(err,results)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(results.rows)
    })
})

 // Mengambil build yang tertera dari buildID
app.get('/getbuildbyID',(req,res)=>{
    db.query(`SELECT * FROM public."Build" WHERE "buildID" ='${req.body.buildID}';`,(err,results)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(results.rows)
    })
})
  ```
  
  Berikutnya adalah API untuk membuat *Build* baru, yaitu dengan mengirimkan query ```INSERT``` ke tabel *Build*.
  
```javascript
app.post('/createbuild',(req,res)=>{
    db.query(`INSERT INTO public."Build" ("buildID",username,char_name,weapon,flower,sands,plume,goblet,circlet)VALUES('${req.body.buildID}'::UUID,'${req.body.char}','${req.body.weapon}','${req.body.flower}','${req.body.sands}','${req.body.plume}','${req.body.goblet}','${req.body.circlet}');`,(err)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(`Registered new Build of '${req.body.char}'`)
    })
})
  ```

</details>
<details>
  <summary>🛠️PersonalBuild</summary>
  
  Tabel database ini berfungsi untuk menyimpan *Build* yang dimiliki oleh suatu *User*. Seluruh API yang tertera disini merepresentasikan seluruh operasi CRUD yang diperlukan untuk menghubungkan keduanya.
  
  API dibawah ini berfungsi untuk menampilkan seluruh *Build* yang dimiliki oleh *User* tersebut.
  ```javascript
app.get('/getpersonalbuildbyuser',(req,res)=>{
    db.query(`SELECT * FROM public."PersonalBuild" WHERE userID = '${req.body.userID}';` ,(err,results)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(results.rows)
    })
})
```
 Untuk operasi Create, berikut adalah APInya untuk membuat *PersonalBuild* baru, dnegan menghubungkan *userID* dan *buildID*, untuk *Build* sendiri perlu dipastikan terlebih dahulu apakah sudah dibuat pada frontend dengan memanggil Create Build pada bagian sebelumnya.
```javascript
app.post('/createpersonal',(req,res)=>{
    db.query(`INSERT INTO public."PersonalBuild" ("personalID",userID,buildID,buildName)VALUES('${req.body.personalID}'::UUID, '${req.body.userID}'::UUID, '${req.body.buildID}'::UUID, '${req.body.buildName}');`,(err)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(`Registered new Personal Build of '${req.body.buildName}'`)
    })
})
```
 Ketika user ingin memperbaharui Buildnya, maka perlu juga operasi untuk mengupdate *PersonalBuild* kembali untuk memastikan *Build* yang dimilikinya sesuai.
```javascript
app.put('/updatepersonal',(req,res)=>{
    db.query(`UPDATE public."PersonalBuild" SET buildID = '${req.body.buildID}'::UUID, userID = '${req.body.userID}'::UUID, buildName = '${req.body.buildName}', WHERE personalID = '${req.body.personalID}'::UUID;`,(err)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(`updated '${req.body.buildName}'`)
    })
})
```
Jika user ingin menghapus *Build* miliknya maka operasi yang dilakukan hanya menghapus satu data saja pada *PersonalBuild* tanpa menghapus datanya pada *Build*. Hal ini dikarenakan lebih dari satu user yang dapat menghubungkan *PersonalBuild*nya ke *Build*, maka ketika dihapus user lain akan kehilangan *Build* tersebut.
```javascript
app.delete('/deletepersonal',(req,res)=>{
    db.query(`DELETE FROM PersonalBuild WHERE personalID = '${req.body.personalID}'::UUID;`,(err)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(`Data build '${req.body.personalID}' berhasil dihapus!`)
    })
})
  ```
  
</details>
<details>
  <summary>👤User</summary>
 
  API untuk mengontrol *User* table hanya terdapat dua, yaitu **Register** dan **Login**
  
  Dibawah ini merupakan API untuk melakukan **Register**, ketika data username dan password tidak ada isinya (blank), maka akan mengembalikan pesan ```'Error: No Username and Password found in body'```
  
  ```javascript
  app.post('/register', async (req, res) => {

    if ( req.body.username != '' && req.body.password != '') {
        const uuid = crypto.randomUUID()

        bcrypt.hash(req.body.password, 10, (err, hashed) => {
            if (err) {
                console.log(err)
                return
            }

            const query = `INSERT INTO public."User" ("userID",username,password) VALUES('${uuid}', '${ req.body.username}', '${hashed}');`
            db.query(query, (err, results) => {
                if (err) {
                    console.log(err)
                    return
                } else {
                    res.send(results.rows)
                }
            })
        })
    } else {
        res.end('Error: No Username and Password found in body')
    }
})
  ```
  API dibawah ini berhubungan dengan **Login**, proses ini dilakukan dengan dua tahap pada frontend, yaitu aktivasi ```session``` terlebih dahulu dengan GET, dilanjutkan POST untuk memeriksa username dan password (dalam bentuk hash)
  ```javascript
app.get('/login', (req, res) => {
    if (req.session.username) {
        res.send({loggedIn: true, username: req.session.username})
    } else {
        res.send({loggedIn: false})
    }
})

app.post('/login', (req, res) => {
    if ( req.body.username != '' && req.body.password != '') {
        const query = `SELECT * FROM public."User" WHERE username = '${req.body.username}';`

        db.query(query, (err, result) => {
            if (err) {
                res.send({ err: err})
            }
            
            bcrypt.compare(req.body.password, result.rows[0].password, (error, response) => {
                if (response) {
                    req.session.username = result.rows[0].username
                    console.log(`logged in as ${req.session.username}`)
                    res.send(result)
                } else {
                    console.log("Error: Password mismatch")
                    // alert("Error: Password mismatch")
                    return res.send({message: "Password Mismatch"})
                }
            })
        })
    } else {
        res.send({err: err})
    }
})
```
  
</details>
<details>
  <summary>📜Other Tables</summary>
  
  Bagian ini memuat API untuk tabel data lainnya yaitu *Character*, *Weapon*, dan *Artifact*. Ketiganya akan mendefinisikan data lengkap dari tabel *Build*.
  API ini berfungsi untuk mengambil data nama dari tiap objek (pada tiap tabel) agar dapat memastikan data *Build* yang dimasukan sesuai dengan objek yang ada. Untuk *Artifact* dipisahkan menjadi lima API sesuai dengan tipe artifactnya.
  ```javascript
  app.get('/charnames',(req,res)=>{
    db.query(`SELECT "char_name" FROM public."Character";` ,(err,results)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(results.rows)
    })
})
app.get('/weaponnames',(req,res)=>{
    db.query(`SELECT "weapon_name" FROM public."Weapon";` ,(err,results)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(results.rows)
    })
})
app.get('/flower',(req,res)=>{
    db.query(`SELECT "artifact_name" FROM public."Artifact" WHERE type='Flower';` ,(err,results)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(results.rows)
    })
})
app.get('/sands',(req,res)=>{
    db.query(`SELECT "artifact_name" FROM public."Artifact" WHERE type='Sands';` ,(err,results)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(results.rows)
    })
})
app.get('/plume',(req,res)=>{
    db.query(`SELECT "artifact_name" FROM public."Artifact" WHERE type='Plume';` ,(err,results)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(results.rows)
    })
})
app.get('/goblet',(req,res)=>{
    db.query(`SELECT "artifact_name" FROM public."Artifact" WHERE type='Goblet';` ,(err,results)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(results.rows)
    })
})
app.get('/circlet',(req,res)=>{
    db.query(`SELECT "artifact_name" FROM public."Artifact" WHERE type='Circlet';` ,(err,results)=>{
        if(err){
            console.log(err)
            return
        }
        res.send(results.rows)
    })
})
  
  ```
  
  
</details>
