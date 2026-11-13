# RideShare — Product Requirements Document (PRD)

## Qëllimi

T’u ndihmojë udhëtarëve që shkojnë në AAB të gjejnë udhëtim me vende të lira dhe t’i dërgojnë shoferit kërkesë për një vend. Shoferi ruan vendimin për pranimin ose refuzimin e kërkesës.

## Përdoruesit

- **Udhëtarët** kërkojnë një udhëtim të përshtatshëm, shohin detajet dhe kërkojnë vend.
- **Shoferët** publikojnë ose menaxhojnë udhëtimet dhe vendosin për kërkesat.

## Kërkesat funksionale

1. Faqja kryesore shfaq listën e udhëtimeve nga databaza; për çdo udhëtim paraqiten të dhënat kryesore, përfshirë nisjen, destinacionin, orën, vendtakimin dhe vendet.
2. Përdoruesi mund të hapë faqen e detajeve të një udhëtimi.
3. Udhëtari mund të dërgojë kërkesë për vend kur udhëtimi ka vende të lira. Kërkesa ka gjendje si në pritje, e pranuar ose e refuzuar; dërgimi nuk garanton rezervim.
4. Shoferi mund të hyjë me GitHub dhe të përdorë panelin e tij. Ndryshimet e udhëtimit lejohen vetëm për shoferin pronar dhe vlejnë validimet përkatëse, si formati i orës.
5. Nëse databaza nuk përgjigjet, ndërfaqja shfaq mesazh gabimi dhe mundësi riprovimi aty ku është ofruar; nuk paraqet mungesën e të dhënave si sukses.
6. Në faqen e detajeve udhëtari mund të zgjedhë dhe konfirmojë lokalisht tekstin e vendtakimit; leja e vendndodhjes kërkohet vetëm pas veprimit të përdoruesit. Kjo zgjedhje nuk dërgohet te shoferi.
7. Përdoruesi i autentikuar mund të ruajë udhëtim te të preferuarat; gjendja dhe numri lexohen nga serveri dhe ruhen në databazë.
8. API-ja e udhëtimeve kthen fushat publike të udhëtimeve dhe përgjigje gabimi kur lista nuk mund të merret.

## MVP

Rrjedha thelbësore është: shfaq listën e udhëtimeve, hap detajet, dërgo kërkesë për një vend kur ka vende të lira dhe lejo shoferin ta shqyrtojë. Kërkesa paraqitet si në pritje derisa shoferi ta pranojë ose refuzojë. Hyrja e shoferit dhe ruajtja e të dhënave në databazë mbështesin këtë rrjedhë.

## Kufizimet dhe jashtë fushës

- Nuk ka pagesa, hartë apo bisedë brenda aplikacionit.
- Kërkesa për vend nuk është rezervim i konfirmuar; konfirmimi varet nga vendimi i shoferit.
- Kërkesa nuk dërgohet kur nuk ka vende të lira.
- Të dhënat e shfaqura për ushtrime janë fiktive, siç tregon faqja kryesore.
- Vendtakimi i zgjedhur lokalisht në veçorinë e Javës 8 nuk ruhet në databazë dhe nuk i dërgohet shoferit.
- Të preferuarat janë personale dhe nuk zëvendësojnë kërkesën për rezervim.

## Kriteret e pranimit

- Vizitori mund të hapë faqen kryesore dhe të shohë udhëtimet e disponueshme me fushat kryesore.
- Zgjedhja e një udhëtimi hap detajet përkatëse.
- Udhëtari mund të kërkojë vend vetëm kur ka vende të lira; kërkesa shfaqet me status dhe nuk quhet e konfirmuar para vendimit të shoferit.
- Shoferi mund të hyjë me GitHub; vetëm shoferi pronar mund të ndryshojë udhëtimin e vet.
- Një orë e pavlefshme nuk ruhet; ora duhet të jetë në formatin `HH:mm`.
- Gabimi i databazës shfaqet si gabim i kuptueshëm dhe jo si listë e rreme bosh ose sukses.
- Veçoritë shtesë respektojnë kufijtë e tyre: vendtakimi mbetet zgjedhje lokale dhe të preferuarat ruhen për përdoruesin e identifikuar.

Kriteret përshkruajnë sjelljen e kërkuar nga implementimi; ky dokument nuk deklaron se janë ekzekutuar teste të reja.
