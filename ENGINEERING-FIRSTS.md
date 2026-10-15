# Engineering Firsts

Выпуск 1. 16.10.2026. Egor Pobozhii. Русская версия, English summary в нижней половине.

Этот документ фиксирует инженерные первые проекта KRUKO, которые не входят в белый лист
(белый лист был про идеи). Здесь железо, провод, код и замеры. Формулировки осторожные,
«насколько нам известно». Что можно проверить, проверяется по ссылкам. Хеш и штамп
OpenTimestamps в цепочке Биткоина лежат рядом с этим документом в этом же репозитории.

## 1. Первый известный бэкпорт контракта Адамара Prism в llama.rn
Дата 12.10.2026.

Свёрнутая тернарная 27B впервые считает внутри React Native приложения. Контракт
`prism.hadamard.*` портирован из мобильной поставки Prism в llama.rn: построение ротаций,
регистрация по весам, активационные трансформы в графе, копии в контекст. Официальный синк
апстрима в llama.rn на тот момент был ещё в пути; в llama.cpp аналогичный рантайм пришёл
11 октября (PR ggml-org/llama.cpp#29600, не наш).

Доказательства: строка «Исторический факт» в README проекта, 12 патчей в
`scripts/patch-llamarn.mjs` с фикстурами и сторожами, журнал замеров и отказов (по запросу).

## 2. Первый живой кластер двух телефонов на кабеле с тернарной 27B
Дата 11 по 15.10.2026, обкаточные прогоны 8 по 16.10.2026.

Ни ПК, ни облака. Мастер и узел соединены проводом, транспорт ggml-rpc, сплит по слоям.
Числа: кабель 37 МБ/с (полка USB 2), сплит 70/30 узел держит 4.36 ГиБ и главный 2.15 ГиБ,
около 2.8 токенов в секунду на первых прогонах. Раскладка по буферам видна числами самого
движка. Приёмка спекуляции: 75 процентов у draft-simple (9 из 12) и 53.1 процента у пары
DSpark на офлайн-полигоне.

Доказательства публичные: gist чисел
https://gist.github.com/figkcoci-debug/76f41c409fc5afe5a431094cc228ddc0 и gist метода
https://gist.github.com/figkcoci-debug/11871f60812d2d18de5214fda3d94160, пост в Reddit
14.10.2026. Внутренние: журнал прогонов и скриншоты (по запросу).

## 3. Первый мобильный спекулятивный декод draft-simple в llama.rn
Дата 11.10.2026.

Классический файловый драфтер в llama.rn: загрузка файла, отдельный контекст со своим KV,
гейты в параллельных слотах и в прямом ходе, жадный сэмплер драфтера. До правок 12 и 13
спекуляция включалась молча и не работала: draft_tokens равен нулю. После: приёмка
75 процентов (9 из 12).

Доказательства: правки 12 и 13 в `scripts/patch-llamarn.mjs`, замер в README проекта.

## 4. Сетевой дефект ggml-rpc найден и починен, апстрим открыт
Дата 14.10.2026.

Stop RPC-сервера не прерывал активного клиента, join ждал вечно, оставались полуоткрытые
сокеты, узел и мастер расходились в разные состояния. Воспроизведено на живом проводе
между телефонами и починено.

Доказательства публичные: https://github.com/ggml-org/llama.cpp/pull/30545 и
https://github.com/ggml-org/llama.cpp/pull/30415 (автор figkcoci-debug), ждут мейнтейнера.

## 5. Первый запуск драфтера DSpark на телефоне
Дата 16.10.2026.

Драфтер dflash со своими головами (марковская, уверенности, GIDD log-SNR) впервые доведён
до загрузки и рабочего контекста на телефоне. Два дефекта найдены до строки и закрыты:
загрузчик не создавал четыре тензора log-SNR (отказ за 0.38 секунды, спекуляция молча
выключалась), и MTP-тип контекста отвергался вратами на модели без nextn-слоёв (молчаливый
ноль вместо ошибки).

Доказательства: правки 18 и 19 в `scripts/patch-llamarn.mjs`, 33 из 33 сторожевых тестов,
хронология вскрытий в журнале (по запросу). Живой замер приёмки на телефоне в работе.

## 6. Метод слепого вскрытия крашей на телефоне без рута
Дата 15 и 16.10.2026.

Краш декодирован от tombstone до буквальной ARM-инструкции, вызвавшей падение, через
addr2line и дизасм собственного собранного `.so`, с восстановлением смысла регистров и
источника мусорного указателя. Этим методом закрыты оба падения второй загрузки модели.

Доказательства: digest-строки и дизассемблерный разбор (по запросу).

---

## English summary

Engineering firsts of the KRUKO project, to our knowledge each is the first of its kind.

1. First known backport of the Prism Hadamard contract into llama.rn: a folded ternary 27B
   model running inside a React Native app (2026-10-12), ahead of the official upstream sync.
2. First live two-phone cable cluster running a ternary 27B: 37 MB/s wire, 70/30 layer split,
   about 2.8 tok/s, 75 percent draft acceptance with draft-simple, 53.1 percent with DSpark
   on an offline testbed (2026-10-11..15).
3. First mobile draft-simple speculative decoding in llama.rn: file drafter, own KV context,
   gates in both decode paths, greedy drafter sampler (2026-10-11).
4. ggml-rpc network defect found, reproduced on a live cable and fixed: server stop must
   interrupt the current client (upstream PRs 30545, 30415).
5. First successful load and working context of a DSpark drafter on a phone: the GIDD
   log-SNR port and the MTP context-type gate, both diagnosed to the exact cause
   (2026-10-16).
6. A root-free crash forensics method on Android: from tombstone to the faulting ARM
   instruction, in use for both second-load crashes (2026-10-15..16).

Evidence links are inline above. SHA-256 digest and OpenTimestamps proof are in this
repository. Text licensed CC BY 4.0. Concepts and results are published as prior art.
