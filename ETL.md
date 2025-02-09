ETL, **Extract, Transform, Load** kelimelerinin kısaltmasıdır ve veriyi bir kaynaktan çekip (Extract), işleyerek uygun hale getirip (Transform), hedef bir veri deposuna yükleme (Load) sürecini ifade eder. Genellikle veri ambarı süreçlerinde kullanılır.

### ETL Sürecinin Adımları:
1. **Extract (Çıkarma)**: 
   - Veriler farklı kaynaklardan (veritabanları, API'ler, dosyalar, bulut servisleri vb.) çekilir.
   - Kaynak sistemlere doğrudan bağlantı kurularak veya batch işlemleriyle veri alınır.

2. **Transform (Dönüştürme)**: 
   - Veriler temizlenir, filtrelenir ve iş kurallarına göre dönüştürülür.
   - Format değişiklikleri, eksik verilerin doldurulması ve veri doğrulama işlemleri yapılır.
   - Veri, hedef sisteme uygun hale getirilir.

3. **Load (Yükleme)**: 
   - Dönüştürülen veriler hedef sisteme (genellikle veri ambarı, OLAP sistemleri veya veri gölleri) yüklenir.
   - Yükleme tam (Full Load) veya kademeli (Incremental Load) olabilir.

Bir **ETL süreci** için basit bir örnek üzerinden ilerleyelim. Diyelim ki elimizde bir **müşteri verisi içeren CSV dosyası** var ve bu verileri bir **veri ambarına yüklemek** istiyoruz. Ancak verileri temizleyip dönüştürmemiz gerekiyor.

---

### **Senaryo:**
Bir e-ticaret şirketi, müşteri bilgilerini içeren bir CSV dosyasından verileri çekerek **PostgreSQL veritabanına yüklemek** istiyor. Ancak, veriler içinde eksik ve hatalı bilgiler var. Bunları düzelterek yükleme yapacağız.

---

## **1. Extract (Çıkarma)**
Öncelikle müşteri bilgilerini içeren CSV dosyasından verileri alıyoruz.

📂 **Müşteri Verisi (customers.csv)**
```
customer_id, name, email, phone, country
101, John Doe, john.doe@example.com, 1234567890, USA
102, Jane Smith, jane.smith@example, 9876543210, UK
103, , david.brown@example.com, , Canada
104, Alice Johnson, alice.johnson@example.com, 1122334455, USA
```
🔹 **Sorunlar**:
- **customer_id eksik değil, ancak bazı isimler boş** (örneğin, müşteri 103).
- **E-posta formatı yanlış olan kayıtlar var** (örneğin, müşteri 102).
- **Telefon numarası eksik olan kayıtlar var** (örneğin, müşteri 103).

---

## **2. Transform (Dönüştürme)**
Bu aşamada verileri temizleyip dönüştürüyoruz:

✅ **Adımlar**:
- Eksik isimleri "Unknown" olarak güncelle.
- Geçersiz e-posta adreslerini filtrele.
- Telefon numarası eksikse "Not Provided" olarak işaretle.

Dönüştürülmüş veri:

```
customer_id, name, email, phone, country
101, John Doe, john.doe@example.com, 1234567890, USA
102, Jane Smith, INVALID_EMAIL, 9876543210, UK
103, Unknown, david.brown@example.com, Not Provided, Canada
104, Alice Johnson, alice.johnson@example.com, 1122334455, USA
```
---

## **3. Load (Yükleme)**
Artık veriyi PostgreSQL veri tabanına yükleyebiliriz. Kullanacağımız tablo:

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255),
    phone VARCHAR(50),
    country VARCHAR(50)
);
```

**SQL INSERT komutları ile yükleme:**
```sql
INSERT INTO customers (customer_id, name, email, phone, country) VALUES
(101, 'John Doe', 'john.doe@example.com', '1234567890', 'USA'),
(102, 'Jane Smith', 'INVALID_EMAIL', '9876543210', 'UK'),
(103, 'Unknown', 'david.brown@example.com', 'Not Provided', 'Canada'),
(104, 'Alice Johnson', 'alice.johnson@example.com', '1122334455', 'USA');
```

---

## **Özet**
✔ **Extract**: CSV dosyasından veriler alındı.  
✔ **Transform**: Eksik ve hatalı veriler düzeltildi.  
✔ **Load**: PostgreSQL veri tabanına temizlenmiş veriler yüklendi.  

**Veri bilimi mülakatlarında ETL ile ilgili sorular**, genellikle **veri temizleme, dönüşüm ve yükleme süreçlerini** nasıl yönettiğinizi anlamaya yöneliktir. Hem teorik bilgi hem de pratik deneyiminiz ölçülür. 

