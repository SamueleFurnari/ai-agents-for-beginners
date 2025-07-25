<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "8164484c16b1ed3287ef9dae9fc437c1",
  "translation_date": "2025-07-23T08:42:43+00:00",
  "source_file": "10-ai-agents-production/README.md",
  "language_code": "tr"
}
-->
# Üretimde AI Ajanları: Gözlemlenebilirlik ve Değerlendirme

AI ajanları deneysel prototiplerden gerçek dünya uygulamalarına geçerken, davranışlarını anlamak, performanslarını izlemek ve çıktıları sistematik olarak değerlendirmek önemli hale gelir.

## Öğrenme Hedefleri

Bu dersi tamamladıktan sonra şunları öğreneceksiniz:
- Ajan gözlemlenebilirliği ve değerlendirme ile ilgili temel kavramlar
- Ajanların performansını, maliyetlerini ve etkinliğini artırma teknikleri
- AI ajanlarınızı sistematik olarak nasıl ve ne şekilde değerlendireceğiniz
- AI ajanlarını üretime alırken maliyetleri nasıl kontrol edeceğiniz
- AutoGen ile oluşturulan ajanları nasıl enstrüman edeceğiniz

Amaç, "kara kutu" ajanlarınızı şeffaf, yönetilebilir ve güvenilir sistemlere dönüştürmek için gerekli bilgiyi size sunmaktır.

_**Not:** Güvenli ve güvenilir AI ajanları dağıtmak önemlidir. [Güvenilir AI Ajanları Oluşturma](./06-building-trustworthy-agents/README.md) dersine göz atmayı unutmayın._

## İzler ve Aralıklar

[Langfuse](https://langfuse.com/) veya [Azure AI Foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/what-is-azure-ai-foundry) gibi gözlemlenebilirlik araçları genellikle ajan çalıştırmalarını izler ve aralıklar olarak temsil eder.

- **İz** bir ajan görevini baştan sona temsil eder (örneğin, bir kullanıcı sorgusunu işleme).
- **Aralıklar** iz içindeki bireysel adımlardır (örneğin, bir dil modeli çağırma veya veri alma).

![Langfuse'de İz Ağacı](https://langfuse.com/images/cookbook/example-autogen-evaluation/trace-tree.png)

Gözlemlenebilirlik olmadan, bir AI ajanı "kara kutu" gibi hissedilebilir - iç durumu ve mantığı opaktır, bu da sorunları teşhis etmeyi veya performansı optimize etmeyi zorlaştırır. Gözlemlenebilirlik ile ajanlar "cam kutulara" dönüşür, bu da güven oluşturmak ve doğru şekilde çalıştıklarından emin olmak için hayati bir şeffaflık sağlar.

## Üretim Ortamlarında Gözlemlenebilirliğin Önemi

AI ajanlarını üretim ortamlarına taşımak, yeni bir dizi zorluk ve gereksinim getirir. Gözlemlenebilirlik artık "olsa iyi olur" değil, kritik bir yetenek haline gelir:

*   **Hata Ayıklama ve Kaynak Analizi**: Bir ajan başarısız olduğunda veya beklenmedik bir çıktı ürettiğinde, gözlemlenebilirlik araçları hatanın kaynağını belirlemek için gerekli izleri sağlar. Bu, birden fazla LLM çağrısı, araç etkileşimleri ve koşullu mantık içerebilen karmaşık ajanlar için özellikle önemlidir.
*   **Gecikme ve Maliyet Yönetimi**: AI ajanları genellikle token veya çağrı başına ücretlendirilen LLM'lere ve diğer harici API'lere dayanır. Gözlemlenebilirlik, bu çağrıların hassas bir şekilde izlenmesini sağlar, aşırı yavaş veya pahalı işlemleri belirlemeye yardımcı olur. Bu, ekiplerin istemleri optimize etmesine, daha verimli modeller seçmesine veya iş akışlarını yeniden tasarlamasına olanak tanır, böylece operasyonel maliyetler yönetilir ve iyi bir kullanıcı deneyimi sağlanır.
*   **Güven, Güvenlik ve Uyumluluk**: Birçok uygulamada, ajanların güvenli ve etik bir şekilde davranmasını sağlamak önemlidir. Gözlemlenebilirlik, ajan eylemleri ve kararlarının bir denetim izini sağlar. Bu, istem enjeksiyonu, zararlı içerik üretimi veya kişisel olarak tanımlanabilir bilgilerin (PII) yanlış kullanımı gibi sorunları tespit etmek ve hafifletmek için kullanılabilir. Örneğin, bir ajanın neden belirli bir yanıt verdiğini veya belirli bir aracı kullandığını anlamak için izleri inceleyebilirsiniz.
*   **Sürekli İyileştirme Döngüleri**: Gözlemlenebilirlik verileri, yinelemeli bir geliştirme sürecinin temelini oluşturur. Ajanların gerçek dünyada nasıl performans gösterdiğini izleyerek, ekipler iyileştirme alanlarını belirleyebilir, modelleri ince ayar yapmak için veri toplayabilir ve değişikliklerin etkisini doğrulayabilir. Bu, çevrimiçi değerlendirmeden elde edilen üretim içgörülerinin çevrimdışı deney ve iyileştirmeyi bilgilendirdiği bir geri bildirim döngüsü oluşturur, böylece ajan performansı giderek daha iyi hale gelir.

## İzlenecek Temel Metrikler

Ajan davranışını izlemek ve anlamak için bir dizi metrik ve sinyal izlenmelidir. Ajanın amacına bağlı olarak spesifik metrikler değişebilir, ancak bazıları evrensel olarak önemlidir.

İşte gözlemlenebilirlik araçlarının izlediği en yaygın metriklerden bazıları:

**Gecikme:** Ajan ne kadar hızlı yanıt veriyor? Uzun bekleme süreleri kullanıcı deneyimini olumsuz etkiler. Ajan çalıştırmalarını izleyerek görevler ve bireysel adımlar için gecikmeyi ölçmelisiniz. Örneğin, tüm model çağrıları için 20 saniye süren bir ajan, daha hızlı bir model kullanılarak veya model çağrıları paralel olarak çalıştırılarak hızlandırılabilir.

**Maliyetler:** Ajan çalıştırma başına maliyet nedir? AI ajanları, token başına ücretlendirilen LLM çağrılarına veya harici API'lere dayanır. Sık araç kullanımı veya birden fazla istem maliyetleri hızla artırabilir. Örneğin, bir ajan, marjinal kalite iyileştirmesi için bir LLM'yi beş kez çağırıyorsa, maliyetin haklı olup olmadığını veya çağrı sayısını azaltıp daha ucuz bir model kullanıp kullanamayacağınızı değerlendirmelisiniz. Gerçek zamanlı izleme, beklenmedik artışları (örneğin, aşırı API döngülerine neden olan hatalar) belirlemeye de yardımcı olabilir.

**İstek Hataları:** Ajan kaç istekte başarısız oldu? Bu, API hatalarını veya başarısız araç çağrılarını içerebilir. Ajanınızı üretimde bu hatalara karşı daha sağlam hale getirmek için geri dönüşler veya yeniden denemeler ayarlayabilirsiniz. Örneğin, LLM sağlayıcı A kapalıysa, yedek olarak LLM sağlayıcı B'ye geçebilirsiniz.

**Kullanıcı Geri Bildirimi:** Doğrudan kullanıcı değerlendirmeleri değerli içgörüler sağlar. Bu, açık derecelendirmeleri (👍beğen/👎beğenme, ⭐1-5 yıldız) veya metin yorumlarını içerebilir. Tutarlı olumsuz geri bildirimler, ajanın beklendiği gibi çalışmadığının bir işareti olarak sizi uyarmalıdır.

**Dolaylı Kullanıcı Geri Bildirimi:** Kullanıcı davranışları, açık derecelendirmeler olmadan bile dolaylı geri bildirim sağlar. Bu, hemen soru yeniden biçimlendirme, tekrarlanan sorgular veya yeniden deneme düğmesine tıklama gibi davranışları içerebilir. Örneğin, kullanıcıların aynı soruyu tekrar tekrar sorduğunu görüyorsanız, bu ajanın beklendiği gibi çalışmadığının bir işaretidir.

**Doğruluk:** Ajan ne sıklıkla doğru veya istenen çıktılar üretiyor? Doğruluk tanımları değişir (örneğin, problem çözme doğruluğu, bilgi alma doğruluğu, kullanıcı memnuniyeti). İlk adım, ajanın başarısının nasıl göründüğünü tanımlamaktır. Doğruluğu otomatik kontroller, değerlendirme puanları veya görev tamamlama etiketleri aracılığıyla izleyebilirsiniz. Örneğin, izleri "başarılı" veya "başarısız" olarak işaretlemek.

**Otomatik Değerlendirme Metrikleri:** Otomatik değerlendirmeler de ayarlayabilirsiniz. Örneğin, bir LLM'yi ajanın çıktısını puanlamak için kullanabilirsiniz, örneğin, yardımcı olup olmadığı, doğru olup olmadığı gibi. Ayrıca, ajanın farklı yönlerini puanlamanıza yardımcı olan birkaç açık kaynak kütüphanesi vardır. Örneğin, RAG ajanları için [RAGAS](https://docs.ragas.io/) veya zararlı dili veya istem enjeksiyonunu tespit etmek için [LLM Guard](https://llm-guard.com/).

Pratikte, bu metriklerin bir kombinasyonu bir AI ajanının sağlığını en iyi şekilde kapsar. Bu bölümün [örnek defterinde](../../../10-ai-agents-production/code_samples/10_autogen_evaluation.ipynb), bu metriklerin gerçek örneklerde nasıl göründüğünü göstereceğiz, ancak önce tipik bir değerlendirme iş akışının nasıl göründüğünü öğreneceğiz.

## Ajanınızı Enstrüman Edin

İzleme verilerini toplamak için kodunuzu enstrüman etmeniz gerekecek. Amaç, ajan kodunu izler ve metrikler yayacak şekilde enstrüman etmek, böylece bunlar bir gözlemlenebilirlik platformu tarafından yakalanabilir, işlenebilir ve görselleştirilebilir.

**OpenTelemetry (OTel):** [OpenTelemetry](https://opentelemetry.io/) LLM gözlemlenebilirliği için endüstri standardı olarak ortaya çıkmıştır. Telemetri verilerini oluşturmak, toplamak ve dışa aktarmak için bir dizi API, SDK ve araç sağlar.

Mevcut ajan çerçevelerini saran ve OpenTelemetry aralıklarını bir gözlemlenebilirlik aracına dışa aktarmayı kolaylaştıran birçok enstrümantasyon kütüphanesi vardır. Aşağıda, [OpenLit enstrümantasyon kütüphanesi](https://github.com/openlit/openlit) ile bir AutoGen ajanını enstrüman etme örneği verilmiştir:

```python
import openlit

openlit.init(tracer = langfuse._otel_tracer, disable_batch = True)
```

Bu bölümdeki [örnek defter](../../../10-ai-agents-production/code_samples/10_autogen_evaluation.ipynb), AutoGen ajanınızı nasıl enstrüman edeceğinizi gösterecektir.

**Manuel Aralık Oluşturma:** Enstrümantasyon kütüphaneleri iyi bir temel sağlar, ancak genellikle daha ayrıntılı veya özel bilgilere ihtiyaç duyulan durumlar vardır. Manuel olarak aralıklar oluşturabilir ve özel uygulama mantığı ekleyebilirsiniz. Daha da önemlisi, otomatik veya manuel olarak oluşturulan aralıkları özel özniteliklerle (etiketler veya meta veriler olarak da bilinir) zenginleştirebilirsiniz. Bu öznitelikler, iş spesifik verileri, ara hesaplamaları veya hata ayıklama veya analiz için yararlı olabilecek herhangi bir bağlamı içerebilir, örneğin `user_id`, `session_id` veya `model_version`.

[Langfuse Python SDK](https://langfuse.com/docs/sdk/python/sdk-v3) ile izler ve aralıklar manuel olarak oluşturma örneği:

```python
from langfuse import get_client
 
langfuse = get_client()
 
span = langfuse.start_span(name="my-span")
 
span.end()
```

## Ajan Değerlendirme

Gözlemlenebilirlik bize metrikler sağlar, ancak değerlendirme, bu verileri analiz etme (ve testler yapma) sürecidir; bir AI ajanının ne kadar iyi performans gösterdiğini ve nasıl geliştirilebileceğini belirler. Başka bir deyişle, bu izler ve metriklere sahip olduğunuzda, ajanı nasıl yargılayacağınızı ve kararlar alacağınızı öğrenirsiniz.

Düzenli değerlendirme önemlidir çünkü AI ajanları genellikle belirli olmayan (non-deterministic) davranışlar sergiler ve zamanla değişebilir (güncellemeler veya model davranışındaki kaymalar yoluyla) – değerlendirme olmadan, "akıllı ajanın" işini iyi yapıp yapmadığını veya gerilediğini bilemezsiniz.

AI ajanları için iki değerlendirme kategorisi vardır: **çevrimiçi değerlendirme** ve **çevrimdışı değerlendirme**. Her ikisi de değerlidir ve birbirini tamamlar. Genellikle çevrimdışı değerlendirme ile başlarız, çünkü bu, herhangi bir ajanı dağıtmadan önce gerekli minimum adımdır.

### Çevrimdışı Değerlendirme

![Langfuse'de Veri Kümesi Öğeleri](https://langfuse.com/images/cookbook/example-autogen-evaluation/example-dataset.png)

Bu, ajanı kontrollü bir ortamda, genellikle test veri kümeleri kullanarak, canlı kullanıcı sorguları olmadan değerlendirmeyi içerir. Beklenen çıktıyı veya doğru davranışı bildiğiniz özenle seçilmiş veri kümeleri kullanırsınız ve ardından ajanı bunlar üzerinde çalıştırırsınız.

Örneğin, bir matematik kelime problemi ajanı oluşturduysanız, bilinen cevaplara sahip [test veri kümesi](https://huggingface.co/datasets/gsm8k) olarak 100 problem içerebilirsiniz. Çevrimdışı değerlendirme genellikle geliştirme sırasında yapılır (ve CI/CD süreçlerinin bir parçası olabilir) ve iyileştirmeleri kontrol etmek veya gerilemelere karşı koruma sağlamak için kullanılır. Avantajı, **tekrarlanabilir olması ve doğru bir şekilde doğruluk metrikleri elde edebilmenizdir, çünkü doğru yanıtlar elinizde vardır**. Ayrıca kullanıcı sorgularını simüle edebilir ve ajanın yanıtlarını ideal cevaplarla karşılaştırabilir veya yukarıda açıklanan otomatik metrikleri kullanabilirsiniz.

Çevrimdışı değerlendirme ile ilgili temel zorluk, test veri kümenizin kapsamlı ve güncel kalmasını sağlamaktır – ajan sabit bir test setinde iyi performans gösterebilir, ancak üretimde çok farklı sorgularla karşılaşabilir. Bu nedenle, test setlerini yeni uç durumlar ve gerçek dünya senaryolarını yansıtan örneklerle güncel tutmalısınız​. Küçük "duman testi" vakaları ve daha büyük değerlendirme setlerinin karışımı faydalıdır: hızlı kontroller için küçük setler ve daha geniş performans metrikleri için daha büyük setler​.

### Çevrimiçi Değerlendirme 

![Gözlemlenebilirlik Metrikleri Genel Bakış](https://langfuse.com/images/cookbook/example-autogen-evaluation/dashboard.png)

Bu, ajanın canlı, gerçek dünya ortamında, yani üretimdeki gerçek kullanım sırasında değerlendirilmesini ifade eder. Çevrimiçi değerlendirme, ajanın gerçek kullanıcı etkileşimleri üzerindeki performansını sürekli olarak izlemeyi ve sonuçları analiz etmeyi içerir.

Örneğin, başarı oranlarını, kullanıcı memnuniyeti puanlarını veya canlı trafik üzerindeki diğer metrikleri izleyebilirsiniz. Çevrimiçi değerlendirmenin avantajı, **laboratuvar ortamında tahmin edemeyeceğiniz şeyleri yakalamasıdır** – zamanla model kaymasını gözlemleyebilir (ajanın etkinliği giriş desenleri değiştikçe azalırsa) ve test verilerinizde olmayan beklenmedik sorguları veya durumları yakalayabilirsiniz​. Bu, ajanın gerçek dünyada nasıl davrandığını gösterir.

Çevrimiçi değerlendirme genellikle dolaylı ve doğrudan kullanıcı geri bildirimlerini toplamayı içerir ve gölge testleri veya A/B testleri (ajanın yeni bir versiyonunun eski versiyonla karşılaştırmak için paralel olarak çalıştırıldığı) yapılabilir. Zorluk, canlı etkileşimler için güvenilir etiketler veya puanlar elde etmenin zor olabilmesidir – kullanıcı geri bildirimlerine veya aşağı akış metriklerine (örneğin, kullanıcı sonucu tıklayıp tıklamadı mı) güvenebilirsiniz.

### İkisini Birleştirme

Çevrimiçi ve çevrimdışı değerlendirmeler birbirini dışlamaz; oldukça tamamlayıcıdırlar. Çevrimiçi izleme içgörüleri (örneğin, ajanın kötü performans gösterdiği yeni tür kullanıcı sorguları) çevrimdışı test veri kümelerini artırmak ve iyileştirmek için kullanılabilir. Tersine, çevrimdışı testlerde iyi performans gösteren ajanlar daha güvenle dağıtılabilir ve çevrimiçi olarak izlenebilir.

Aslında, birçok ekip bir döngü benimser:

_çevrimdışı değerlendir -> dağıt -> çevrimiçi izle -> yeni hata vakalarını topla -> çevrimdışı veri kümesine ekle -> ajanı iyileştir -> tekrarla_.

## Yaygın Sorunlar

AI ajanlarını üretime alırken çeşitli zorluklarla karşılaşabilirsiniz. İşte bazı yaygın sorunlar ve olası çözümleri:

| **Sorun**    | **Olası Çözüm**   |
| ------------- | ------------------ |
| AI Ajanı görevleri tutarlı bir şekilde yerine getiremiyor | - AI Ajanına verilen istemi iyileştirin; hedefleri netleştirin.<br>- Görevleri alt görevlere bölmek ve bunları birden fazla ajan tarafından ele almak faydalı olabilir. |
| AI Ajanı sürekli döngülere giriyor  | - Ajanın süreci ne zaman durduracağını bilmesi için açık sonlandırma şartları ve koşulları sağlayın.<br>- Akıl yürütme ve planlama gerektiren karmaşık görevler için, akıl yürütme görevleri için uzmanlaşmış daha büyük bir model kullanın. |
| AI Ajanı araç çağrıları iyi performans göstermiyor   | - Aracın çıktısını ajan sisteminin dışında test edin ve doğrulayın. |

- Tanımlanan parametreleri, istemleri ve araçların isimlendirmesini iyileştirin.  |
| Çoklu ajan sistemi tutarlı bir şekilde çalışmıyor | - Her bir ajana verilen istemleri, birbirinden belirgin ve spesifik olacak şekilde iyileştirin.<br>- Hangi ajanın doğru olduğunu belirlemek için bir "yönlendirme" veya kontrol ajanı kullanarak hiyerarşik bir sistem oluşturun. |

Bu sorunların çoğu, gözlemlenebilirlik sağlandığında daha etkili bir şekilde tespit edilebilir. Daha önce tartıştığımız izleme ve metrikler, ajan iş akışında sorunların tam olarak nerede meydana geldiğini belirlemeye yardımcı olur, bu da hata ayıklama ve optimizasyonu çok daha verimli hale getirir.

## Maliyet Yönetimi

AI ajanlarını üretime alırken maliyetleri yönetmek için bazı stratejiler:

**Daha Küçük Modeller Kullanmak:** Küçük Dil Modelleri (SLM'ler), belirli ajansal kullanım durumlarında iyi performans gösterebilir ve maliyetleri önemli ölçüde azaltır. Daha önce belirtildiği gibi, performansı daha büyük modellerle karşılaştırmak ve değerlendirmek için bir sistem oluşturmak, SLM'nin kullanım durumunuzda ne kadar iyi performans göstereceğini anlamanın en iyi yoludur. SLM'leri niyet sınıflandırma veya parametre çıkarımı gibi daha basit görevler için kullanmayı düşünün ve daha karmaşık akıl yürütme için büyük modelleri saklayın.

**Yönlendirme Modeli Kullanmak:** Benzer bir strateji, farklı model çeşitlerini ve boyutlarını kullanmaktır. Talepleri karmaşıklığa göre en uygun modellere yönlendirmek için bir LLM/SLM veya sunucusuz işlev kullanabilirsiniz. Bu, maliyetleri azaltırken doğru görevlerde performansı da garanti eder. Örneğin, basit sorguları daha küçük, daha hızlı modellere yönlendirin ve yalnızca karmaşık akıl yürütme görevleri için pahalı büyük modelleri kullanın.

**Yanıtları Önbelleğe Almak:** Yaygın talepleri ve görevleri belirlemek ve yanıtları ajansal sisteminizden geçmeden önce sağlamak, benzer taleplerin hacmini azaltmanın iyi bir yoludur. Daha temel AI modelleri kullanarak bir talebin önbelleğe alınmış taleplerinize ne kadar benzediğini belirlemek için bir akış bile uygulayabilirsiniz. Bu strateji, sıkça sorulan sorular veya yaygın iş akışları için maliyetleri önemli ölçüde azaltabilir.

## Hadi bunu pratikte görelim

Bu bölümün [örnek not defterinde](../../../10-ai-agents-production/code_samples/10_autogen_evaluation.ipynb), ajanımızı izlemek ve değerlendirmek için gözlemlenebilirlik araçlarını nasıl kullanabileceğimize dair örnekler göreceğiz.

## Önceki Ders

[Metakognisyon Tasarım Deseni](../09-metacognition/README.md)

## Sonraki Ders

[MCP](../11-mcp/README.md)

**Feragatname**:  
Bu belge, AI çeviri hizmeti [Co-op Translator](https://github.com/Azure/co-op-translator) kullanılarak çevrilmiştir. Doğruluk için çaba göstersek de, otomatik çevirilerin hata veya yanlışlık içerebileceğini lütfen unutmayın. Belgenin orijinal dili, yetkili kaynak olarak kabul edilmelidir. Kritik bilgiler için profesyonel insan çevirisi önerilir. Bu çevirinin kullanımından kaynaklanan yanlış anlamalar veya yanlış yorumlamalar için sorumluluk kabul etmiyoruz.