# 🔥 Redis Project

Bu proje, **Redis** ile çalışan bir **.NET Core** uygulamasıdır. Redis kullanarak **önbellekleme (caching), mesaj kuyruğu (queueing)** ve **gerçek zamanlı veri yönetimi** sağlanır.

![Redis Logo](https://upload.wikimedia.org/wikipedia/commons/6/6b/Redis_Logo.svg)

---

## 🚀 Özellikler

✅ **Redis Desteği**: Hızlı ve verimli önbellekleme mekanizması.  
✅ **StackExchange.Redis Kütüphanesi** ile güçlü Redis bağlantısı.  
✅ **Veri Saklama ve Okuma** işlemleri için kolay kullanım.  
✅ **Pub/Sub Mekanizması** ile mesaj kuyruğu desteği.  
✅ **Kolay Entegrasyon** ile hızlı başlangıç.

---

## 📂 Proje Mimarisi

```
RedisProject/
│-- Controllers/
│   ├── CacheController.cs
│-- Services/
│   ├── RedisService.cs
│-- Program.cs
│-- Startup.cs
│-- README.md
```

---

## 🛠 Kurulum

Projeyi klonladıktan sonra aşağıdaki adımları takip edin:

### 1️⃣ Projeyi Klonlayın

```sh
git clone https://github.com/aliikara/Redis.git
cd Redis
```

### 2️⃣ Gerekli Bağımlılıkları Yükleyin

```sh
dotnet add package StackExchange.Redis
dotnet restore
```

### 3️⃣ Redis Bağlantısını Ayarlayın

Eğer Redis sunucusu çalışmıyorsa, terminalde şu komutu kullanarak başlatın:

```sh
docker run --name redis-server -d -p 6379:6379 redis
```

### 4️⃣ Uygulamayı Çalıştırın

```sh
dotnet run
```

---

## 📌 Kullanım

### 🔹 Redis’e Veri Yazma
```http
POST http://localhost:5000/cache/set
Body: { "key": "username", "value": "Ali" }
```

### 🔹 Redis’ten Veri Okuma
```http
GET http://localhost:5000/cache/get?key=username
```

### 🔹 Redis Key Silme
```http
DELETE http://localhost:5000/cache/delete?key=username
```

---

## 🎯 Örnek Kodlar

### 📌 **Redis Service Tanımlama**
```csharp
﻿using StackExchange.Redis;

namespace Redis.Sentinel.Services
{
    public class RedisService
    {
        static ConfigurationOptions sentinelOptions => new()
        {
            EndPoints =
            {
                { "localhost", 6393 },
                { "localhost", 6394 },
                { "localhost", 6395 }
            },
            CommandMap = CommandMap.Sentinel,
            AbortOnConnectFail = false
        };
        static ConfigurationOptions masterOptions => new()
        {
            AbortOnConnectFail = false
        };
        static public async Task<IDatabase> RedisMasterDatabase()
        {
            ConnectionMultiplexer sentinelConnection = await ConnectionMultiplexer.SentinelConnectAsync(sentinelOptions);

            System.Net.EndPoint masterEndPoint = null;
            foreach (System.Net.EndPoint endpoint in sentinelConnection.GetEndPoints())
            {
                IServer server = sentinelConnection.GetServer(endpoint);
                if (!server.IsConnected)
                    continue;
                masterEndPoint = await server.SentinelGetMasterAddressByNameAsync("mymaster");
                break;
            }

            var localMasterIP = masterEndPoint.ToString() switch
            {
                "0.0.0.0:6379" => "localhost:6379",
                "0.0.0.0:6379" => "localhost:6390",
                "0.0.0.0:6379" => "localhost:6391",
                "0.0.0.0:6379" => "localhost:6392",
            };

            ConnectionMultiplexer masterConnection = await ConnectionMultiplexer.ConnectAsync(localMasterIP);
            IDatabase database = masterConnection.GetDatabase();
            return database;
        }
    }
}
```

### 📌 **Redis Kullanımı Controller Üzerinde**
```csharp
﻿using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Redis.Sentinel.Services;

namespace Redis.Sentinel.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class RedisController : ControllerBase
    {
        //localhost:4200/api/redis/setvalue/name/ali
        [HttpGet("[action]/{key}/{value}")]
        public async Task<IActionResult> SetValue(string key, string value)
        {
            var redis = await RedisService.RedisMasterDatabase();
            await redis.StringSetAsync(key, value);
            return Ok();
        }
        //localhost:4200/api/redis/getvalue/name
        [HttpGet("[action]/{key}")]
        public async Task<IActionResult> GetValue(string key)
        {
            var redis = await RedisService.RedisMasterDatabase();
            var data = await redis.StringGetAsync(key);
            return Ok(data.ToString());
        }
    }
}
```

---


## ❓ Sıkça Sorulan Sorular (SSS)

### ❓ Redis nedir?
Redis, açık kaynaklı, bellek içi bir veri deposudur. Önbellekleme, mesaj kuyruğu ve veritabanı olarak kullanılabilir.

### ❓ Redis nasıl çalıştırılır?
Eğer Redis yüklü değilse Docker üzerinden başlatabilirsiniz:
```sh
docker run --name redis-server -d -p 6379:6379 redis
```

### ❓ Redis hangi senaryolarda kullanılır?
- Önbellekleme (Caching)
- Gerçek zamanlı mesaj kuyruğu (Pub/Sub)
- Oturum yönetimi
- Veri saklama ve hızlı erişim

---

## 📜 Lisans

📄 Bu proje **MIT Lisansı** ile lisanslanmıştır.

---

## 📞 İletişim
🚀 **Geliştirici:** [Ali](https://github.com/aliikara)  
📧 **E-posta:** [seninemail@example.com](mailto:alii.kara@icloud.com)  
🌍 **Projeyi İncele:** [GitHub Repo](https://github.com/aliikara/Redis)

🛠 **Katkıda bulunmak isterseniz,** pull request gönderebilirsiniz! ✨

