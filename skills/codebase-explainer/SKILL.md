---
name: codebase-explainer
description: Explain a codebase or flow as an interactive active-learning HTML page: step-through flows, predict-then-reveal quizzes, collapsible depth. For onboarding, 'explain this code', 'bu projeyi anlat'.
---

# Codebase Explainer

Amaç: kişinin kafasında **hiçbir boşluk kalmadan**, sistemi zihninde canlandırarak anlamasını sağlamak. Bu, pasif okumayla olmaz — kişi akışı kendisi yürütmeli, tahmin etmeli, yanılmalı, düzeltmeli. Üç problem, üç çözüm:

1. **İçerik karmaşası** → Feynman tekniği (düz dil + benzetme + izlenebilir gerçek örnek)
2. **Sunum karmaşası** → katmanlı sunum (özet hep açık, detay tıklayınca)
3. **Pasif okuma** → aktif öğrenme mekanizmaları (adım adım oynatıcı + tahmin soruları)

## Çekirdek: Üç Aktif Öğrenme Mekanizması

Varsayılan çıktı, tek dosyalık etkileşimli bir HTML sayfası. İçinde:

**1. Adım adım akış oynatıcısı** — en önemli akış (örn. "kullanıcı satın aldığında ne olur") statik diyagram olarak DEĞİL, kişinin "İleri/Geri" butonlarıyla kendisinin yürüttüğü bir animasyon olarak verilir. Her adımda: aktif bileşen vurgulanır, geçilmiş bileşenler işaretli kalır, o adımda tam olarak ne olduğu 1-2 cümleyle anlatılır. Kişi isteğin sistemde nereye gittiğini gözüyle izler — uygulama kafasında böyle canlanır.

**2. Tahmin-sonra-cevap soruları** — 2-4 soru, her biri sistemin davranışıyla ilgili bir senaryo sorar ("X servisi çökmüşse ne olur?"). Kişi önce tahmin eder (şıkka tıklar), sonra doğru cevap + neden açıklaması görünür. Sorular ezber değil **davranış** sorgulamalı: "hangi dosyada?" değil, "bu durumda sistem ne yapar?". Yanlış şıklar makul olmalı — bariz yanlış şık öğretmez.

**3. Katmanlı derinlik** — hiçbir detay silinmez, katlanır:
- Katman 1 (hep açık): ne işe yarar + genel resim
- Katman 2 (tıklayınca açılır): her akış/servis/konu kendi bölümünde
- Katman 3 (bölüm içinde ayrıca katlı, "Teknik detaylar"): dosya adları, edge case'ler, kırılgan noktalar, bulunan buglar — istediğin kadar derin

## Yazım Kuralı: Feynman Üçlüsü

Her açıklama bloğu için:
1. **Düz dil** — jargon yoksa hiç kullanma, varsa hemen yanında açıkla
2. **Bir benzetme** — mekanizmanın gerçek davranışını yansıtan, günlük hayattan (message queue → restoranın sipariş fişi; JWT → etkinlik bileziği; gateway → binanın giriş kapısı gibi)
3. **İsimli, somut örnek** — "kullanıcı" değil "Ali"; "bir istek" değil "sepetindeki 2 ürün". Soyut "bu modül şunu yönetir" cümlesi yasak.

## Adım 0: Mod ve Format

- Genel istek ("bu projeyi anlat") → **Tam Anlatım**: oynatıcı en kritik akış için, quiz tüm sistemi kapsar, her servis bir Katman 2 bölümü
- Spesifik soru ("X nasıl çalışıyor") → **Odaklı Cevap**: oynatıcı o akış için, 1-2 soru, tek konu — kapsam dışına çıkma
- Format: sohbet ortamında **etkileşimli HTML** (varsayılan). Kullanıcı repo'ya kaydedilecek dosya isterse **Markdown + `<details>` katmanları + mermaid** (GitHub/GitLab native render eder; oynatıcı ve quiz markdown'da olmaz, onların yerine sequence diagram + "kendine sor" soruları düz metin olarak girer).

## Adım 1: Keşif — Sadece İhtiyacın Olanı Oku

- Dizin yapısı + paket dosyasından tech stack
- Giriş noktalarını bul (route'lar, main/index)
- Büyük/çoklu servisli projede: önce her servisin sadece giriş noktası, sonra servisler arası sınırlar (HTTP mi kuyruk mu paylaşılan DB mi), sonra en kritik 1-2 akışı uçtan uca gerçekten oku
- **Her teknik iddiayı kodda grep ile doğrula.** Oynatıcıdaki her adım, quiz'deki her doğru cevap gerçek koda dayanmalı. Doğrulayamadığını yazma.
- Odaklı Cevap'ta sadece soruyla ilgili kısmı tara

## Adım 2: HTML'i Kur

Çalışan tam örnek: `references/interactive-example.html` — 4 servisli bir örnek projeye uygulanmış hali; oynatıcının, quiz'in ve katmanların çalışan kodunu içerir. Bunu şablon olarak kullan, içeriği projeye göre değiştir. Teknik notlar:

- Tek dosya, vanilla JS — framework yok, CDN bağımlılığı yok (oynatıcı ve quiz saf JS/CSS; mermaid gerekiyorsa cdnjs'ten yüklenebilir ama oynatıcı zaten mermaid'den daha iyi anlattığı için ana akışta gerek kalmaz)
- Katmanlar native `<details>/<summary>` (erişilebilir, JS'siz)
- Oynatıcı: bileşenler yatay kutu dizisi, `steps` array'inde her adımın hangi bileşeni vurguladığı + anlatım metni; İleri/Geri butonları + ilerleme sayacı
- Quiz: her soru bir kart; şıkka tıklayınca doğru yeşil, seçilen yanlışsa kırmızı, açıklama görünür
- Sade görünüm: ~760px okuma genişliği, tek vurgu rengi, dekorasyon yok

Oynatıcı adımları yazarken: her adım tek bir olayı anlatsın; adım sayısı 4-8 arası ideal; anlatım metni o adımda **neyin neden olduğunu** söylesin, sadece "A B'ye istek atar" değil.

## Adım 3: Diyagram (gerekirse)

Oynatıcı zaten ana akışı anlattığı için diyagram ihtiyacı azalır. Yine de genel mimari resmi veya ikincil akışlar için gerekiyorsa `references/diagram-guide.md`'deki tip seçimine uy. 6-8 düğümü aşan diyagramı böl.

## Adım 4: Kaydet / Göster

- HTML → `present_files` ile göster (chat artifact'i); kullanıcı isterse diske de kaydet
- Markdown (repo'ya) → küçük proje: `docs/PROJECT_GUIDE.md`; büyük proje: `docs/PROJECT_GUIDE.md` + `docs/services/<ad>.md`; odaklı cevap: `docs/qna/<konu>.md`
- Var olan dosyanın üzerine yazmadan önce sor

## Son Kontrol

- Oynatıcıyı baştan sona yürüten biri, akışı kimseye sormadan bir başkasına anlatabilir mi?
- Quiz soruları davranış mı sorguluyor, ezber mi? Yanlış şıklar makul mü?
- Katman 1'i okuyan (hiç tıklamayan) biri temel fikri anlar mı? Katman 3'e inen her detayı bulur mu?
- Her iddia kodda doğrulandı mı?
- Herhangi bir yer "rapor" gibi hissettiriyor mu — ediyorsa yeniden yaz

Biri "hayır" ise düzelt, sonra teslim et.
