# Diyagram Seçim Rehberi

Doğru diyagram tipini seçmek, yanlış tipi güzelleştirmekten daha önemli. Aşağıdaki tablo hangi durumda hangisini kullanacağını gösterir.

| Ne anlatıyorsun | Mermaid tipi | Kullan çünkü |
|---|---|---|
| Genel mimari, modüller arası ilişki | `flowchart` (TD veya LR) | Kutular + oklar, en okunaklı genel bakış |
| Bir isteğin zaman içindeki yolculuğu (A çağırır B'yi, B çağırır C'yi) | `sequenceDiagram` | Kim kimi, hangi sırada çağırıyor — en net gösterim |
| Veritabanı şeması, tablolar arası ilişki | `erDiagram` | İlişkisel yapı için standart |
| Sınıflar/interface'ler arası OOP ilişkisi | `classDiagram` | Kalıtım, kompozisyon net görünür |
| Bir varlığın durumdan duruma geçişi (sipariş: beklemede→onaylandı→kargoda) | `stateDiagram-v2` | Durum makineleri için doğru araç |
| Zaman çizelgesi / release akışı | `gitGraph` veya basit `flowchart LR` | Kronoloji |

**Kural: Bir diyagramda en fazla 8-10 düğüm olsun.** Daha fazlası gerekiyorsa, diyagramı böl (örn. "üst seviye mimari" + ayrı "auth alt akışı").

## Örnekler

### Mimari (flowchart)

```mermaid
flowchart LR
    User[Kullanıcı] -->|HTTP isteği| API[API Gateway]
    API --> Auth[Auth Servisi]
    API --> Orders[Sipariş Servisi]
    Orders --> DB[(PostgreSQL)]
    Orders -->|event| Queue[Kuyruk]
    Queue --> Email[E-posta Servisi]
```

Notlar:
- `[(...)]` veritabanı şekli, `[...]` normal kutu, `{...}` karar noktası
- Yön: mimari için genelde `LR` (soldan sağa) daha okunaklı, süreç akışları için `TD` (yukarıdan aşağı)
- Etiketli oklar (`-->|event|`) neyin aktığını gösterir, sadece "bağlı" demez

### İstek akışı (sequenceDiagram)

```mermaid
sequenceDiagram
    participant U as Kullanıcı
    participant F as Frontend
    participant A as API
    participant D as Veritabanı

    U->>F: "Kaydet" butonuna tıklar
    F->>A: POST /orders
    A->>A: Validasyon
    A->>D: INSERT order
    D-->>A: order_id
    A-->>F: 201 Created
    F-->>U: Başarı mesajı gösterir
```

Notlar:
- `->>` senkron çağrı, `-->>` cevap/dönüş
- Her ok gerçek bir kod satırına/fonksiyona karşılık gelmeli, uydurma
- Katılımcı (`participant`) sayısı 6'yı geçmesin, geçiyorsa akışı böl

### Durum makinesi (stateDiagram-v2)

```mermaid
stateDiagram-v2
    [*] --> Beklemede
    Beklemede --> Onaylandı: ödeme başarılı
    Beklemede --> İptal: kullanıcı iptal etti
    Onaylandı --> Kargoda: depo hazırladı
    Kargoda --> Teslim: kurye teslim etti
    İptal --> [*]
    Teslim --> [*]
```

Notlar:
- Geçiş etiketleri (`: ödeme başarılı`) neyin tetiklediğini gösterir — bu olmadan diyagram sadece kutu-ok yığını olur
- `[*]` başlangıç/bitiş noktası
