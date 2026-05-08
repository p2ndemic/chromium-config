# Групповые политики Chrome и Brave
**Контекст:** Безопасность + Производительность + Максимальная приватность (без передачи данных)
**Платформа:** Arch Linux (Google Chrome / Brave Browser)

---

## Содержание
1. [Chrome — Общие настройки](#chrome--общие-настройки)
2. [Chrome — Сеть и HTTPS](#chrome--сеть-и-https)
3. [Chrome — Загрузки и кэш](#chrome--загрузки-и-кэш)
4. [Chrome — Аккаунт и синхронизация](#chrome--аккаунт-и-синхронизация)
5. [Chrome — Репортинг (Cloud Reporting)](#chrome--репортинг-cloud-reporting)
6. [Chrome — AI-функции](#chrome--ai-функции)
7. [Chrome — Захват и шаринг](#chrome--захват-и-шаринг)
8. [Chrome — Геолокация и куки](#chrome--геолокация-и-куки)
9. [Chrome — Производительность](#chrome--производительность)
10. [Chrome — Safe Browsing](#chrome--safe-browsing)
11. [Chrome — Печать и доступ](#chrome--печать-и-доступ)
12. [Chrome — Устаревшие (Deprecated)](#chrome--устаревшие-deprecated)
13. [Brave — Защитные функции (включить)](#brave--защитные-функции-включить)
14. [Brave — Brave Shields](#brave--brave-shields)
15. [Brave — Отключить (телеметрия и лишние функции)](#brave--отключить-телеметрия-и-лишние-функции)
16. [Brave — Устаревшие (Deprecated)](#brave--устаревшие-deprecated)

---

## Chrome — Общие настройки

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `BookmarkBarEnabled` | Отображение панели закладок | `true` / `false` / `not set` | `not set` | Нейтральная политика, пользователь решает сам |
| `PromotionsEnabled` | Промо-контент и приветственные страницы Chrome | `true` / `false` | `false` | Заменяет устаревший `PromotionalTabsEnabled`; подталкивает к входу в Google-аккаунт |
| `EnableExperimentalPolicies` | Разрешить применение экспериментальных политик | Список строк / `not set` | `not set` | Google предупреждает о риске потери данных браузера и нестабильности |
| `BackgroundModeEnabled` | Chrome работает в фоне после закрытия всех окон | `true` / `false` | `false` | Уровень: browser (machine GPO); фоновые расширения продолжают отправлять запросы |
| `EnableMediaRouter` | Google Cast (трансляция на Chromecast/TV) | `true` / `false` | `false` | Требует перезапуска; взаимодействует с облачной инфраструктурой Google |
| `FeedbackSurveysEnabled` | Встроенные опросы Google в браузере | `true` / `false` | `false` | Автоматически отключается при `MetricsReportingEnabled = false` |
| `UserFeedbackAllowed` | Отправка отчётов об ошибках в Google через меню | `true` / `false` | `false` | Отчёт включает скриншот экрана, URL и системную информацию |
| `MediaRecommendationsEnabled` | Персонализированные рекомендации медиаконтента | `true` / `false` | `false` | Сбор поведенческих данных для персонализации |
| `DesktopSharingHubEnabled` | Кнопка «Поделиться» в адресной строке и меню | `true` / `false` | `false` | Точка доступа к внешним сервисам шаринга |
| `ShoppingListEnabled` | Отслеживание цен на товары через Google Shopping | `true` / `false` | `false` | URL страниц товаров передаются в Google Shopping API |
| `SideSearchEnabled` | Последняя поисковая выдача в боковой панели | `true` / `false` | `false` | Требует перезапуска; держит постоянное соединение с поисковиком |
| `GoogleSearchSidePanelEnabled` | Поиск Google в боковой панели на любой странице | `true` / `false` | `false` | Контекст страницы передаётся в Google; аналог `AIModeSettings` |
| `ScrollToTextFragmentEnabled` | Скролл к тексту по URL-фрагменту (`#:~:text=`) | `true` / `false` | `false` | Требует перезапуска; timing attack позволяет проверить наличие текста на странице |
| `PaymentMethodQueryEnabled` | Разрешить сайтам проверять наличие платёжных методов | `true` / `false` | `false` | Fingerprinting-вектор: сайт получает информацию о наличии карт до действия пользователя |
| `SiteSearchSettings` | Корпоративные поисковые ярлыки в адресной строке | Словарь (name, shortcut, url) | Настроить | Только при наличии домена (AD/MDM/Enterprise); данные не передаются — локальная конфигурация |
| `URLBlocklist` | Блокировка доступа к списку URL | Список строк (URL-паттерны) | Настроить | С Chrome 147 `*` не блокирует `chrome://`; рекомендуется добавить `accounts.google.com`; до 1000 паттернов |
| `ProfileSeparationSettings` | Разделение управляемого и личного профиля | `0` (Suggest) / `1` (Enforce) / `2` (Disabled) | `2` | Только Google Admin Console; не работает на machine-level; не актуально при `BrowserSignin = 0` |

---

## Chrome — Сеть и HTTPS

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `DnsOverHttpsMode` | Режим DNS-over-HTTPS | `off` / `automatic` / `secure` | `secure` | Уровень: browser; **обязательно** задать в паре с `DnsOverHttpsTemplates` |
| `DnsOverHttpsTemplates` | URI-шаблон DoH-резолвера | URI-строка (например, `https://dns.quad9.net/dns-query`) | Задать явно | Обязателен при `DnsOverHttpsMode = secure`; без него DNS упадёт; Quad9 — рекомендован для публичного использования |
| `HttpsOnlyMode` | Принудительный режим HTTPS | `allowed` / `disallowed` / `force_enabled` / `force_balanced_enabled` | `force_enabled` | Внутренние HTTP-сервисы добавить в `HttpAllowlist`; работает в паре с `HttpsUpgradesEnabled` |
| `HttpsUpgradesEnabled` | Автоматический апгрейд HTTP → HTTPS | `true` / `false` | `true` | Дополняет `HttpsOnlyMode`; данные не передаются — только логика выбора протокола |
| `HttpAllowlist` | Исключения из HTTPS-апгрейда (HTTP-хосты) | Список строк (hostnames / паттерны) | Настроить | Blanket wildcard `*` и `[*]` запрещены; IPv6 в скобках: `[::1]`; не применяется к HSTS |
| `WebRtcIPHandling` | Управление IP-адресами, раскрываемыми WebRTC | `default` / `default_public_and_private_interfaces` / `default_public_interface_only` / `disable_non_proxied_udp` | `disable_non_proxied_udp` | Предотвращает WebRTC IP leak (утечка реального IP при VPN); компромисс: ухудшает качество WebRTC-звонков |
| `QuicAllowed` | Разрешить протокол QUIC (HTTP/3) | `true` / `false` | `true` | Требует перезапуска; уровень: browser; данные не "передаются" — это транспортный протокол; отключать только при проблемах UDP в сети |
| `NetworkPredictionOptions` | DNS prefetch, TCP preconnect, prerender | `0` (всегда) / `2` (никогда) | `2` | Значение `1` удалено с Chrome 52 (обрабатывается как `0`); запросы к URL, которые пользователь не открыл |
| `IPv6ReachabilityOverrideEnabled` | Принудительные AAAA-запросы для всех хостов | `true` / `false` | `not set` | Уровень: browser; менять только при диагностированных проблемах с IPv6-резолвингом |
| `DomainReliabilityAllowed` | Телеметрия надёжности соединений → Google | `true` / `false` | `false` | Требует перезапуска; уровень: browser; без явного `false` следует за `MetricsReportingEnabled` |

---

## Chrome — Загрузки и кэш

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `DownloadRestrictions` | Ограничения загрузки файлов (Safe Browsing) | `0` / `1` / `2` / `3` / `4` | `4` | `4` = блокировать только подтверждённо вредоносные; значения `1` и `2` дополнительно блокируют по типу файла и передают метаданные в Google |
| `CacheEncryptionEnabled` | Шифрование HTTP-кэша на диске | `true` / `false` | `true` | Chrome 146+; небольшой I/O overhead; защищает кэш от чтения при физическом доступе к диску |
| `DiskCacheSize` | Максимальный размер дискового кэша (байты) | Integer (байты) | `209715200` (200 MB) | Требует перезапуска; уровень: browser; значение — hint, реальный размер выше; для SSD рекомендуется 200 MB; работает в паре с `CacheEncryptionEnabled` |
| `DiskCacheDir` | Директория дискового кэша | Строка (путь) | Опционально | Требует перезапуска; уровень: browser; не указывать корневой раздел; полезно для RAM-диска или зашифрованного тома |
| `BlockThirdPartyCookies` | Блокировка сторонних cookie | `true` / `false` | `true` | Тест SSO/SAML перед внедрением; исключения через `CookiesAllowedForUrls`; не применяется в Инкогнито |

---

## Chrome — Аккаунт и синхронизация

| Политика                  | Краткое описание                                              | Доступные значения                         | Рекомендуемое значение | Примечание                                                                                                                                                          |
| ------------------------- | ------------------------------------------------------------- | ------------------------------------------ | ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `BrowserSignin`           | Вход в браузер с Google-аккаунтом                             | `0` (Disable) / `1` (Enable) / `2` (Force) | `0`                    | Требует перезапуска; уровень: browser; **фундамент изоляции** — без этого все ограничения можно обойти; пользователи всё ещё могут войти в Google-сервисы на сайтах |
| `SyncDisabled`            | Отключить Chrome Sync (история, пароли, закладки, расширения) | `true` / `false`                           | `true`                 | Работает в паре с `BrowserSignin = 0`; не совмещать с `RoamingProfileSupportEnabled`                                                                                |
| `PasswordSharingEnabled`  | Шаринг паролей через Google Family Service                    | `true` / `false`                           | `false`                | Пароли передаются через облачный Family Service Google; зависит от `SyncEnabled`                                                                                    |
| `SharedClipboardEnabled`  | Буфер обмена между устройствами через Google Sync             | `true` / `false`                           | `false`                | Требует `BrowserSignin` + Sync (уже отключили); явный `false` как defence-in-depth                                                                                  |
| `TabGroupSharingSettings` | Совместная работа с группами вкладок                          | `0` (Allow) / `1` (Disallow)               | `1`                    | Только Google Admin Console; URL и заголовки вкладок передаются через облако Google                                                                                 |

---

## Chrome — Репортинг (Cloud Reporting)

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `CloudReportingEnabled` | Базовый канал Cloud Reporting → Google Admin | `true` / `false` | `false` | Уровень: browser; зависимость для `CloudExtensionRequestEnabled` и `CloudReportingUploadFrequency` |
| `CloudExtensionRequestEnabled` | Запросы на установку расширений в Google Admin | `true` / `false` | `false` | Только Google Admin Console; зависит от `CloudReportingEnabled` |
| `CloudProfileReportingEnabled` | Cloud Reporting на уровне профиля | `true` / `false` | `false` | Только cloud user policy; зависит от `CloudReportingEnabled` |
| `CloudReportingUploadFrequency` | Частота отправки отчётов (часы) | `3–24` | Не актуально | Бессмысленно при `CloudReportingEnabled = false` |
| `UserSecuritySignalsReporting` | Security signals профиля → сервер управления | `true` / `false` | `false` | Только cloud user policy; зависит от `CloudReportingEnabled` |
| `UserSecurityAuthenticatedReporting` | First-party auth при отправке security signals | `true` / `false` | `false` | Зависит от `UserSecuritySignalsReporting`; без него не работает |
| `ReportVersionData` | Отчёт о версии браузера | `true` / `false` | `false` | Уровень: browser; defence-in-depth при `CloudReportingEnabled = false` |
| `ReportPolicyData` | Отчёт о применённых политиках | `true` / `false` | `false` | Уровень: browser; defence-in-depth при `CloudReportingEnabled = false` |
| `ReportMachineIDData` | Отчёт об идентификаторе машины | `true` / `false` | `false` | Уровень: browser; defence-in-depth при `CloudReportingEnabled = false` |
| `ReportUserIDData` | Отчёт об идентификаторе пользователя | `true` / `false` | `false` | Уровень: browser; defence-in-depth при `CloudReportingEnabled = false` |
| `ReportExtensionsAndPluginsData` | Отчёт об установленных расширениях | `true` / `false` | `false` | Уровень: browser; defence-in-depth при `CloudReportingEnabled = false` |
| `ReportSafeBrowsingData` | Отчёт о данных Safe Browsing | `true` / `false` | `false` | Уровень: browser; defence-in-depth при `CloudReportingEnabled = false` |
| `MetricsReportingEnabled` | Телеметрия использования и краш-репорты → Google | `true` / `false` | `false` | Требует перезапуска; уровень: browser; автоматически отключает `FeedbackSurveysEnabled` |

---

## Chrome — AI-функции

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `GenAiDefaultSettings` | Мастер-рубильник всех GenAI-функций Chrome | `0` (Allow + обучение) / `1` (Allow без обучения) / `2` (Disable all) | `2` | **Главная AI-политика**: закрывает все текущие и будущие GenAI-функции по умолчанию; только Google Admin Console |
| `GeminiSettings` | Интеграция Gemini в браузер | `0` (Allow) / `1` (Disable) | `1` | Отключается при `GenAiDefaultSettings = 2`; явное значение как defence-in-depth |
| `AIModeSettings` | AI Mode в адресной строке и новой вкладке | `0` (Allow) / `1` (Disable) | `1` | Отключается при `GenAiDefaultSettings = 2`; отдельно от `SearchContentSharingSettings` |
| `SearchContentSharingSettings` | Шаринг содержимого страниц с Google AI Mode/Lens | `0` (Allow) / `1` (Disable) | `1` | Не зависит от `AIModeSettings`; контролирует шаринг через боковую панель |
| `AutofillPredictionSettings` | GenAI для автозаполнения форм | `0` / `1` / `2` | `2` | Данные форм отправляются в Google AI; отключается при `GenAiDefaultSettings = 2` |
| `DevToolsGenAiSettings` | AI в Chrome DevTools (консоль, стеки, сетевые запросы) | `0` / `1` / `2` | `2` | Стек-трейсы и фрагменты кода уходят в Google; отключается при `GenAiDefaultSettings = 2` |
| `HistorySearchSettings` | AI-поиск по истории браузера с анализом контента | `0` / `1` / `2` | `2` | Передаёт содержимое страниц, не только URL; отключается при `GenAiDefaultSettings = 2` |

---

## Chrome — Захват и шаринг

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `ScreenCaptureAllowed` | Доступ сайтов к захвату экрана | `true` / `false` | `false` | Исключения через `ScreenCaptureAllowedByOrigins`; не применяется если сайт есть в `TabCaptureAllowedByOrigins` |
| `AudioCaptureAllowed` | Доступ сайтов к микрофону | `true` / `false` | `false` | Whitelist через `AudioCaptureAllowedUrls`; влияет на все аудиовходы |
| `VideoCaptureAllowed` | Доступ сайтов к камере | `true` / `false` | `false` | Whitelist через `VideoCaptureAllowedUrls`; влияет на все видеовходы |
| `TabCaptureAllowedByOrigins` | Whitelist захвата вкладок | Список URL-паттернов | Не задавать | **Обходит `ScreenCaptureAllowed = false`** — пустой список обязателен |
| `LiveCaptionEnabled` | Живые субтитры (обработка локальная) | `true` / `false` | `false` | Обработка локальная, но при первом включении загружает модель от Google; зависимость для `LiveTranslateEnabled` |
| `LiveTranslateEnabled` | Перевод субтитров → серверы Google | `true` / `false` | `false` | Явная передача субтитров в Google; автоматически отключается при `LiveCaptionEnabled = false` |

---

## Chrome — Геолокация и куки

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `DefaultGeolocationSetting` | Доступ сайтов к физическому местоположению | `1` (Allow) / `2` (Block) / `3` (Ask) | `2` | Whitelist для конкретных URL через другие политики |
| `PreciseGeolocationAllowedForUrls` | Whitelist точной геолокации без запроса | Список URL-паттернов | Не задавать | **Обходит `DefaultGeolocationSetting = 2`** — любой URL в списке получает геолокацию без ведома пользователя |
| `UrlKeyedAnonymizedDataCollectionEnabled` | Сбор URL посещаемых страниц → Google | `true` / `false` | `false` | "Anonymized" означает отсутствие привязки к аккаунту, но не то, что Google не видит URL |
| `UrlKeyedMetricsAllowed` | Метрики с привязкой к URL | `true` / `false` | `false` (вероятно) | Документация не предоставлена; по названию аналогично `UrlKeyedAnonymizedDataCollectionEnabled` |

---

## Chrome — Производительность

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `HighEfficiencyModeEnabled` | Режим экономии памяти (Memory Saver) | `true` / `false` | `true` | Уровень: browser; **обязательная пара** для `MemorySaverModeSavings` — без неё тот параметр не работает |
| `MemorySaverModeSavings` | Агрессивность выгрузки неактивных вкладок | `0` (Moderate) / `1` (Balanced) / `2` (Maximum) | `1` | Уровень: browser; работает только при `HighEfficiencyModeEnabled = true` |
| `ChromeDataRegionSetting` | Географический регион хранения данных в Google | `0` (No preference) / `1` (US) / `2` (EU) | Не актуально | Только Google Admin Console; требует перезапуска; при отключённой передаче данных — не актуально |

---

## Chrome — Safe Browsing

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `SafeBrowsingProtectionLevel` | Уровень защиты Safe Browsing | `0` (Off) / `1` (Standard) / `2` (Enhanced) | `1` | **Используй вместо устаревшей `SafeBrowsingEnabled`**; режим `2` (Enhanced) передаёт полные URL в Google |
| `SafeBrowsingExtendedReportingEnabled` | Расширенная отчётность Safe Browsing | `true` / `false` | `false` | Передаёт системную информацию и контент страниц в Google |
| `TranslateEnabled` | Встроенный переводчик Chrome | `true` / `false` | `false` | Полное содержимое страницы отправляется в Google Translate |

---

## Chrome — Печать и доступ

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `PrintingEnabled` | Разрешить печать из браузера | `true` / `false` | `true` | Нет передачи данных; отключать только при специальном требовании |
| `SilentPrintingEnabled` | Мгновенная печать без диалога подтверждения | `true` / `false` | `false` | Уровень: browser; риск случайной или злонамеренной печати конфиденциальных документов |
| `RemoteDebuggingAllowed` | Удалённая отладка через CDP (`--remote-debugging-port`) | `true` / `false` | `false` | Требует перезапуска; уровень: browser; CDP даёт полный доступ к браузеру: cookies, DOM, JS, перехват трафика |
| `PasswordLeakDetectionEnabled` | Проверка паролей по базе утечек | `true` / `false` | `false` | Хеш пары login+password отправляется в Google; альтернатива — корп. менеджер паролей |

---

## Chrome — Устаревшие (Deprecated)

| Политика | Краткое описание | Статус | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `SafeBrowsingEnabled` | Включить Safe Browsing | Deprecated с Chrome 83 | **Не использовать** | Заменена на `SafeBrowsingProtectionLevel`; игнорируется если задана новая политика |
| `PromotionalTabsEnabled` | Промо-контент на полной вкладке | Deprecated | **Не использовать** | Заменена на `PromotionsEnabled` |
| `ForceSafeSearch` | Принудительный SafeSearch в Google и YouTube | Deprecated | **Не использовать** | Заменена на `ForceGoogleSafeSearch` + `ForceYouTubeRestrict`; актуальна только для контент-фильтрации |

---

## Brave — Защитные функции (включить)

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `BraveGlobalPrivacyControlEnabled` | Сигнал GPC (`Sec-GPC: 1`) — "не продавать мои данные" | `true` / `false` | `true` | **Юридически значимый стандарт** (CCPA, частично GDPR); Dynamic refresh; уникальная функция Brave |
| `BraveDebouncingEnabled` | Защита от bounce tracking (пропуск трекинговых редиректов) | `true` / `false` | `true` | Dynamic refresh; работает в паре с `BlockThirdPartyCookies = true` |
| `BraveDeAmpEnabled` | Обход Google AMP — переход к оригинальному издателю | `true` / `false` | `true` | Dynamic refresh; Google не видит посещение AMP-страниц; часто ускоряет загрузку на десктопе |
| `BraveReduceLanguageEnabled` | Защита от fingerprinting по языковым настройкам (`Accept-Language`) | `true` / `false` | `true` | Dynamic refresh; работает в паре с `DefaultBraveFingerprintingV2Setting = 3` |
| `BraveTrackingQueryParametersFilteringEnabled` | Удаление трекинговых GET-параметров из URL (`fbclid`, `utm_*`, `gclid`) | `true` / `false` | `true` | Dynamic refresh; только при включённых Shields; дополняет `BraveDebouncingEnabled` |

---

## Brave — Brave Shields

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `DefaultBraveAdblockSetting` | Блокировка рекламы и трекеров | `1` (AllowAds) / `2` (BlockAds) | `2` | Dynamic refresh; фундамент Shields; `BraveShieldsEnabledForUrls` имеет приоритет |
| `DefaultBraveFingerprintingV2Setting` | Защита от fingerprinting (рандомизация Canvas, WebGL, AudioContext) | `1` (Disable) / `3` (Standard Mode) | `3` | Dynamic refresh; работает в паре с `BraveReduceLanguageEnabled = true` |
| `DefaultBraveHttpsUpgradeSetting` | HTTPS-апгрейд в Shields | `1` (Allow HTTP) / `2` (Strict) / `3` (Standard) | `2` | Dynamic refresh; дублирует `HttpsOnlyMode` на уровне Shields — defence-in-depth |
| `DefaultBraveReferrersSetting` | Ограничение Referer-заголовка для permissive policy | `1` (Allow unsafe-url) / `2` (Cap to strict-origin-when-cross-origin) | `2` | Dynamic refresh; кросс-доменные запросы видят только origin, без пути и параметров |
| `DefaultBraveRemember1PStorageSetting` | Хранение first-party storage между сессиями | `1` (Remember) / `2` (Forget) | `1` | Dynamic refresh; значение `2` удаляет данные при закрытии вкладки — пользователь выходит со всех сайтов |
| `BraveShieldsEnabledForUrls` | Принудительно включить Shields для конкретных URL | Список URL-паттернов | Настроить | Уровень: browser (per_profile: false); нет dynamic refresh; приоритет выше пользовательских настроек |
| `BraveShieldsDisabledForUrls` | Принудительно отключить Shields для конкретных URL | Список URL-паттернов | Настроить (только внутренние) | Уровень: browser; нет dynamic refresh; **использовать только для внутр. ресурсов** где Shields мешает |
| `TorDisabled` | Отключить Tor в приватном окне Brave | `true` (Disable) / `false` (Allow) | `false` | ⚠️ Инвертированная логика: `false` = НЕ отключать; уровень: browser; Tor — инструмент приватности |

---

## Brave — Отключить (телеметрия и лишние функции)

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `BraveP3AEnabled` | Privacy Preserving Product Analytics (аналитика использования) | `true` / `false` | `false` | Уровень: browser (per_profile: false); нет dynamic refresh; данные уходят на серверы Brave |
| `BraveStatsPingEnabled` | Пинг активности для подсчёта пользователей → Brave | `true` / `false` | `false` | Уровень: browser; нет dynamic refresh; закрывает телеметрию вместе с `BraveP3AEnabled = false` |
| `BraveWebDiscoveryEnabled` | Передача данных о страницах для улучшения Brave Search | `true` / `false` | `false` | Нет dynamic refresh; по умолчанию отключена — явный `false` для надёжности |
| `BraveAIChatEnabled` | AI-ассистент Leo (запросы на серверы Brave) | `true` / `false` | `false` | Нет dynamic refresh; содержимое страниц отправляется для обработки моделью |
| `BraveNewsDisabled` | Новостная лента на новой вкладке | `true` (Disable) / `false` (Enable) | `true` | ⚠️ Инвертированная логика: `true` = ОТКЛЮЧИТЬ; нет dynamic refresh |
| `BraveRewardsDisabled` | BAT-вознаграждения за просмотр рекламы Brave | `true` (Disable) / `false` (Enable) | `true` | ⚠️ Инвертированная логика: `true` = ОТКЛЮЧИТЬ; нет dynamic refresh |
| `BraveTalkDisabled` | Видеозвонки Brave Talk (Jitsi) | `true` (Disable) / `false` (Enable) | `true` | ⚠️ Инвертированная логика: `true` = ОТКЛЮЧИТЬ; нет dynamic refresh |
| `BraveVPNDisabled` | Brave VPN (платный сервис Guardian) | `true` (Disable) / `false` (Enable) | `true` | ⚠️ Инвертированная логика: `true` = ОТКЛЮЧИТЬ; не влияет на внешние VPN-клиенты (Mullvad, WireGuard) |
| `BraveWalletDisabled` | Встроенный Web3-кошелёк (ETH, BTC, SOL) | `true` (Disable) / `false` (Enable) | `true` | ⚠️ Инвертированная логика: `true` = ОТКЛЮЧИТЬ; запросы к RPC-узлам блокчейнов |
| `BraveWaybackMachineEnabled` | Интеграция с Wayback Machine при ошибке 404 | `true` / `false` | `false` | Нет dynamic refresh; 404 URL передаётся на серверы archive.org |
| `BravePlaylistEnabled` | Сохранение медиа для офлайн-просмотра | `true` / `false` | `false` | Нет dynamic refresh; сканирует DOM страниц на медиа-элементы |
| `BraveSyncUrl` | URL сервера синхронизации Brave | URI-строка / `not set` | `not set` | Нет dynamic refresh; синхронизация уже отключена через `SyncDisabled = true`; опционально при self-hosted сервере |
| `BraveSpeedreaderEnabled` | Режим чтения (упрощение статей) | `true` / `false` | `not set` | Нет dynamic refresh; обработка локальная, данные не передаются |
| `EmailAliasesEnabled` | Псевдонимы email для анонимной регистрации | `true` / `false` | `not set` | Нет dynamic refresh; запросы уходят на серверы Brave; доступна с Chrome 147+ |

---

## Brave — Устаревшие (Deprecated)

| Политика | Краткое описание | Статус | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| `IPFSEnabled` | Поддержка IPFS-протокола | `deprecated: true` | **Не использовать** | Помечена устаревшей в официальной документации Brave |

---

## Сводка: инвертированная логика в Brave

Следующие политики Brave имеют **инвертированную логику** — `true` означает **отключить**:

| Политика | true означает |
|---|---|
| `BraveNewsDisabled` | Отключить Brave News |
| `BraveRewardsDisabled` | Отключить Brave Rewards |
| `BraveTalkDisabled` | Отключить Brave Talk |
| `BraveVPNDisabled` | Отключить Brave VPN |
| `BraveWalletDisabled` | Отключить Brave Wallet |
| `TorDisabled` | Отключить Tor (`false` = оставить доступным) |

---

## Ключевые связи между политиками

| Политика | Зависит от / Связана с |
|---|---|
| `CloudExtensionRequestEnabled` | Требует `CloudReportingEnabled = true` |
| `CloudProfileReportingEnabled` | Требует `CloudReportingEnabled = true` |
| `CloudReportingUploadFrequency` | Не имеет смысла без `CloudReportingEnabled` |
| `UserSecuritySignalsReporting` | Требует `CloudReportingEnabled` |
| `UserSecurityAuthenticatedReporting` | Требует `UserSecuritySignalsReporting = true` |
| `LiveTranslateEnabled` | Автоотключается при `LiveCaptionEnabled = false` |
| `FeedbackSurveysEnabled` | Автоотключается при `MetricsReportingEnabled = false` |
| `MemorySaverModeSavings` | Не работает без `HighEfficiencyModeEnabled = true` |
| `DnsOverHttpsTemplates` | **Обязателен** при `DnsOverHttpsMode = secure` |
| `HttpAllowlist` | Исключения для `HttpsOnlyMode` и `HttpsUpgradesEnabled` |
| `PreciseGeolocationAllowedForUrls` | **Обходит** `DefaultGeolocationSetting = 2` |
| `TabCaptureAllowedByOrigins` | **Обходит** `ScreenCaptureAllowed = false` |
| `BraveTrackingQueryParametersFilteringEnabled` | Работает только при включённых Brave Shields |
| `DefaultBraveAdblockSetting` | `BraveShieldsEnabledForUrls` имеет приоритет |
| `DefaultBraveFingerprintingV2Setting` | Дополняет `BraveReduceLanguageEnabled` |
| `DefaultBraveHttpsUpgradeSetting` | Дублирует `HttpsOnlyMode` на уровне Shields (defence-in-depth) |
| `PasswordSharingEnabled` | Зависит от `SyncDisabled` и `BrowserSignin` |
| `SharedClipboardEnabled` | Зависит от `BrowserSignin` и `SyncDisabled` |
| `GenAiDefaultSettings` | Мастер-значение для всех AI-политик без явной настройки |
| `BraveSyncUrl` | Актуален только если Sync не отключён через `SyncDisabled` |
| `BraveStatsPingEnabled` | В паре с `BraveP3AEnabled` закрывает всю телеметрию Brave |
