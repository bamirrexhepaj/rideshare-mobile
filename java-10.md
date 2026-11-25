# RideShare — Java 10

## Çfarë ndërtova

Shtova rrjedhën server-side për kërkesat e vendeve në aplikacionin ekzistues Next.js. Udhëtari dërgon kërkesë me sesionin NextAuth; PostgreSQL/Neon e ruan me `ride_id`, `passenger_id` dhe status `pending` ose `accepted`. Shoferi pronar lexon kërkesat e udhëtimit të vet. Udhëtari lexon vetëm kërkesën e vet. UI rilexon gjendjen çdo 5 sekonda, mban pamjen e fundit kur leximet dështojnë dhe provon sërish menjëherë kur shfletuesi raporton rikthimin online.

Pranimi bëhet përmes funksionit PostgreSQL `prano_kerkesen`: kyç kërkesën dhe udhëtimin, verifikon shoferin pronar dhe rolin, kontrollon vendet, pastaj ndryshon statusin dhe ul vendet në të njëjtin thirrje/transaction. Unique constraint ndalon kërkesë të dytë nga i njëjti udhëtar për të njëjtin udhëtim. Përgjigjet personale përdorin `private, no-store`; identiteti merret në server nga sesioni.

## Përgatitja e provës

Në repository nuk ka udhëtime të reja të krijuara posaçërisht për Javën 10. ID-të `1`, `2` dhe `3` gjenden në seed-in shembullor `schema.sql`, por lidhja e tyre aktuale me shoferin dhe numri i vendeve në Neon nuk u verifikuan gjatë kësaj pune. Nuk u përdorën llogari testimi ose kredenciale. Asnjë ID udhëtimi nuk raportohet si e testuar.

## Provat që bëra

### Prova 1: Rasti 1 · Kërkesa mbërrin te shoferi

- **Hapat:** Plani i provës ishte të hapej i njëjti udhëtim me dy sesione të izoluara, të dërgohej kërkesa si udhëtar, të rifreskohej faqja një herë dhe të inspektohej rreshti në Neon; pastaj të pritej polling-u te shoferi pa Refresh.
- **Çfarë prisja:** Rreshti `pending` të ekzistonte pas rifreskimit; shoferi ta shihte automatikisht; të dy ekranet të tregonin dy vende.
- **Çfarë pa realisht shoferi dhe udhëtari, pa Refresh:** **NOT VERIFIED.** Nuk u ekzekutua provë me dy sesione. Në këtë mjedis nuk u gjet konfigurim `DATABASE_URL` ose llogari testimi për Neon/NextAuth.
- **Ku e verifikova ruajtjen:** Nuk u verifikua në Neon. Migrimi SQL është i përfshirë, por nuk u aplikua.

### Prova 2: Rasti 2 · Pranimi përditëson të dy ekranet

- **Hapat dhe vendet para/pas:** Plani ishte nisja me dy vende dhe kërkesë `pending`, pranimi nga shoferi pronar, pastaj verifikimi i gjendjes në dy dritaret.
- **Përsëritja e pranimit dhe prova me 0 vende:** Nuk u ekzekutuan ndaj databazës. Funksioni SQL i shkruar kthen `already_accepted` pa zbritje të dytë dhe `no_seats` kur vendet janë zero; këto garanci nuk u ekzekutuan në Neon gjatë kësaj pune.
- **Çfarë pashë realisht te të dy përdoruesit:** **NOT VERIFIED.** Nuk pati sesione të izoluara ose përgjigje nga serveri real.

### Prova 3: Rasti 3 · Ndërprerja dhe rilidhja

- **ID-ja e udhëtimit të ri të provës, cila dritare ishte offline dhe si e provova:** Nuk u krijua udhëtim i dytë dhe DevTools offline nuk u përdor. **NOT VERIFIED.** Nuk ka ID për të raportuar.
- **Pamja e fundit gjatë ndërprerjes:** Nuk u vëzhgua në shfletues. Kodi ruan gjendjen e fundit në state të komponentit, shfaq “Po rilidhemi · të dhënat mund të jenë të vjetra” dhe çaktivizon veprimet kur eventi `offline` ose dështimi i leximit shënon lidhjen joaktive.
- **Çfarë ndryshoi pas rilidhjes:** Nuk u vëzhgua. Kodi nis lexim të menjëhershëm në eventin `online`; mesazhi i rilidhjes hiqet vetëm pas leximit të suksesshëm.

## Ku gjendet puna

- `aplikacioni/schema-java10.sql`: migrimi i tabelës unike të kërkesave dhe funksioni atomik i pranimit. **Nuk u aplikua në Neon** sepse ky workspace nuk kishte `DATABASE_URL`.
- `aplikacioni/src/lib/kerkesat.ts`: lexim i ndarë sipas rolit, krijim idempotent dhe thirrje e funksionit të pranimit.
- `aplikacioni/src/app/api/udhetimet/[id]/kerkesa/route.ts`: API e autentikuar, me përgjigje `private, no-store`.
- `aplikacioni/src/components/KerkesaUdhetimi.tsx`: statusi, veprimet dhe polling-u 5 sekondësh me ruajtje të gjendjes së fundit dhe rilidhje.
- `aplikacioni/src/app/udhetimi/[id]/page.tsx` dhe `aplikacioni/src/app/udhetimi/[id]/kerkesa/page.tsx`: integrimi në faqet ekzistuese.
- Nuk u shtuan varësi.

## Kontrollet lokale

- `npm.cmd test`: PASS, 13/13 teste në 6 skedarë.
- `npm.cmd run lint`: PASS.
- `npm.cmd run build`: PASS; faqet dhe Route Handler-i i ri u kompajluan.
- Serveri `npm.cmd run dev -- -H 0.0.0.0 -p 3000` është aktiv. Kërkesa lokale nga laptopi mori HTTP 200 në `http://localhost:3000/` dhe `http://10.193.216.123:3000/`.
- Laptop: `http://localhost:3000/`. Telefon në të njëjtin Wi-Fi: `http://10.193.216.123:3000/`. Adresa LAN u lexua nga `ipconfig`; qasja fizike nga telefoni **NOT VERIFIED**.
- Këto kontrolle provojnë build-in dhe faqen fillestare, jo integrimin me Neon. Tri provat me dy sesione mbeten **NOT VERIFIED**.

## Prova me kolegun

**Nuk u krye.** Nuk pati hyrje të dytë ose demonstrim me koleg gjatë kësaj pune.

## Çfarë mbetet për përmirësim

Migrimi duhet aplikuar në Neon-in e autorizuar; duhen përgatitur dy profile testimi me shofer pronar dhe udhëtar; pastaj duhen ekzekutuar tri provat në dy dritare, përfshirë autorizimin, përsëritjen e pranimit, rastin me zero vende dhe DevTools offline/online. LAN-i dhe telefoni fizik nuk u provuan. Feedback-u i GitHub-it ende nuk është marrë dhe do të plotësohet pas dorëzimit manual.

## Ndihma nga AI (Artificial Intelligence – inteligjencë artificiale)

U përdor Codex për inspektimin e projektit, implementimin minimal, migrimin SQL, raportin dhe ekzekutimin e testeve/build-it. Codex nuk kishte kredenciale Neon ose dy sesione testimi; për këtë arsye nuk deklarohen rezultate reale të tri provave dhe nuk pretendohet demonstrim me kolegun. Studenti duhet ta aplikojë migrimin në databazën e autorizuar, të kryejë provat dhe të plotësojë këtë raport me vëzhgimet reale.

## Pas dorëzimit të parë — përditëso më vonë

Feedback-u i GitHub-it ende nuk është marrë dhe do të plotësohet pas dorëzimit manual. Nuk pretendohet rezultat 2/2 para kontrollit real të botit.
