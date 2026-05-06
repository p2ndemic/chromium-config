# Chrome Group Policy — Рекомендации по безопасности и приватности

> **Контекст:** Максимальная приватность и безопасность. Никаких данных в Google.  
> **Дата составления:** 2026-05-07  
> **Версия Chrome:** 144+ (часть политик требуют конкретных минимальных версий — см. примечания)

---

## Условные обозначения

| Символ | Значение |
|--------|----------|
| 🔴 | Критически важно — влияет на безопасность или передачу данных |
| 🟡 | Рекомендуется — повышает уровень защиты |
| 🟢 | Производительность / нейтральное |
| ⚠️ | Требует аккуратной настройки или тестирования |
| ⛔ | Устаревшая политика — не использовать |

---

## 1. Аккаунт и синхронизация

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🔴 `BrowserSignin` | Вход пользователя в браузер с Google-аккаунтом | `0` = запрещён, `1` = разрешён, `2` = принудительный | `0` | Применяется на уровне browser (machine GPO). Требует перезапуска. Связан с `SyncDisabled` |
| 🔴 `SyncDisabled` | Синхронизация истории, паролей, закладок, вкладок с Google | `true` = отключить Sync, `false` = разрешить | `true` | Связан с `BrowserSignin`. Несовместим с `RoamingProfileSupportEnabled` |
| 🟡 `ProfileSeparationSettings` | Разделение управляемого и личного профиля Chrome | `0` = предложить, `1` = принудить, `2` = отключить | `2` | Только через Google Admin Console. Не работает на machine-level. При `BrowserSignin = 0` не актуально |
| 🟡 `SharedClipboardEnabled` | Передача буфера обмена между устройствами через Google Sync | `true` = включить, `false` = выключить | `false` | Требует `BrowserSignin` и `Sync` — уже отключены. Defence-in-depth |

---

## 2. Генеративный AI (GenAI)

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🔴 `GenAiDefaultSettings` | Мастер-рубильник для всех AI-функций Chrome | `0` = включить + улучшать модели, `1` = включить без улучшения, `2` = отключить | `2` | Только через Google Admin Console. Автоматически отключает все дочерние AI-политики, не настроенные явно. Связан с `GeminiSettings`, `AIModeSettings`, `AutofillPredictionSettings`, `HistorySearchSettings`, `DevToolsGenAiSettings`, `SearchContentSharingSettings` |
| 🔴 `GeminiSettings` | Интеграция Gemini в браузер | `0` = разрешить, `1` = запретить | `1` | Передача данных в облако Google. Отключается автоматически при `GenAiDefaultSettings = 2` |
| 🔴 `AIModeSettings` | AI Mode в адресной строке и на новой вкладке | `0` = разрешить, `1` = запретить | `1` | Связан с `GoogleSearchSidePanelEnabled` и `SearchContentSharingSettings`. Отключается при `GenAiDefaultSettings = 2` |
| 🔴 `SearchContentSharingSettings` | Шаринг содержимого страниц с Google AI Mode и Lens | `0` = разрешить, `1` = запретить | `1` | Независим от `AIModeSettings` — отключает контекстный шаринг через боковую панель |
| 🔴 `AutofillPredictionSettings` | GenAI для автозаполнения форм | `0` = вкл. + улучшать модели, `1` = вкл. без улучшения, `2` = отключить | `2` | Передаёт данные форм в Google AI. Отключается при `GenAiDefaultSettings = 2` |
| 🔴 `DevToolsGenAiSettings` | AI в Chrome DevTools (Console Insights, AI assistance) | `0` = вкл. + улучшать модели, `1` = вкл. без улучшения, `2` = отключить | `2` | Передаёт ошибки, стек-трейсы, код, сетевые запросы в Google. Отключается при `GenAiDefaultSettings = 2` |
| 🔴 `HistorySearchSettings` | AI-поиск по содержимому истории браузера | `0` = вкл. + улучшать модели, `1` = вкл. без улучшения, `2` = отключить | `2` | Передаёт URL и содержимое страниц, а не только заголовки. Отключается при `GenAiDefaultSettings = 2` |

---

## 3. Cloud Reporting

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🔴 `CloudReportingEnabled` | Главный переключатель облачной отчётности Chrome | `true` = включить, `false` = выключить | `false` | Базовая зависимость для `CloudExtensionRequestEnabled` и `CloudReportingUploadFrequency`. Применяется на уровне browser |
| 🔴 `CloudProfileReportingEnabled` | Облачная отчётность на уровне профиля пользователя | `true` = включить, `false` = выключить | `false` | Передаёт версию ОС/браузера, расширения, политики. Только cloud user policy |
| 🔴 `CloudExtensionRequestEnabled` | Отправка запросов на установку расширений в Google Admin | `true` = включить, `false` = выключить | `false` | Требует `CloudReportingEnabled = true`. Только Google Admin Console |
| 🔴 `CloudReportingUploadFrequency` | Частота отправки отчётов в часах | `3`–`24` | не актуально | Бессмысленно при `CloudReportingEnabled = false` |
| 🔴 `UserSecuritySignalsReporting` | Отправка сигналов безопасности устройства в Google | `true` = включить, `false` = выключить | `false` | Связан с `UserSecurityAuthenticatedReporting`. Только cloud user policy |
| 🔴 `UserSecurityAuthenticatedReporting` | First-party аутентификация при отправке security signals | `true` = включить, `false` = выключить | `false` | Требует `UserSecuritySignalsReporting = true` |
| 🟡 `ReportVersionData` | Отправка данных о версии в Google Admin | `true` = включить, `false` = выключить | `false` | Defence-in-depth поверх `CloudReportingEnabled = false`. Применяется на уровне browser (machine GPO) |
| 🟡 `ReportPolicyData` | Отправка данных о применённых политиках | `true` = включить, `false` = выключить | `false` | Defence-in-depth поверх `CloudReportingEnabled = false` |
| 🟡 `ReportMachineIDData` | Отправка идентификатора машины | `true` = включить, `false` = выключить | `false` | Defence-in-depth поверх `CloudReportingEnabled = false` |
| 🟡 `ReportUserIDData` | Отправка идентификатора пользователя | `true` = включить, `false` = выключить | `false` | Defence-in-depth поверх `CloudReportingEnabled = false` |
| 🟡 `ReportExtensionsAndPluginsData` | Отправка данных об установленных расширениях | `true` = включить, `false` = выключить | `false` | Defence-in-depth поверх `CloudReportingEnabled = false` |
| 🟡 `ReportSafeBrowsingData` | Отправка данных Safe Browsing | `true` = включить, `false` = выключить | `false` | Defence-in-depth поверх `CloudReportingEnabled = false` |

---

## 4. Сеть и DNS

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🔴 `DnsOverHttpsMode` | Режим DNS-over-HTTPS | `off`, `automatic`, `secure` | `secure` | ⚠️ Требует обязательной настройки `DnsOverHttpsTemplates`. Без него DNS упадёт |
| 🔴 `DnsOverHttpsTemplates` | URI-шаблон DoH-резолвера | Строка URI | Свой или Quad9: `https://dns.quad9.net/dns-query` | Обязателен при `DnsOverHttpsMode = secure`. Связан с `DnsOverHttpsMode` |
| 🔴 `WebRtcIPHandling` | Ограничение IP-адресов, раскрываемых WebRTC | `default`, `default_public_and_private_interfaces`, `default_public_interface_only`, `disable_non_proxied_udp` | `disable_non_proxied_udp` | ⚠️ Может снизить качество WebRTC-звонков. Компромисс: `default_public_interface_only` |
| 🟢 `QuicAllowed` | Разрешить протокол QUIC (HTTP/3) | `true` = разрешить, `false` = запретить | `true` | Это транспортный протокол, а не канал передачи данных. Отключать только при сетевых проблемах с UDP |
| 🟢 `HttpsUpgradesEnabled` | Автоматический апгрейд HTTP → HTTPS | `true` = включить, `false` = выключить | `true` | Дополняет `HttpsOnlyMode`. Связан с `HttpsOnlyMode` |
| 🟢 `HttpsOnlyMode` | Принудительный HTTPS для всех навигаций | `allowed`, `disallowed`, `force_enabled`, `force_balanced_enabled` | `force_enabled` | ⚠️ Внутренние HTTP-сервисы перестанут открываться — добавь в `HttpAllowlist`. Связан с `HttpAllowlist` |
| ⚠️ `HttpAllowlist` | Список хостов, исключённых из HTTPS-принудиловки | Список строк (хосты/паттерны) | Настроить минимально | Не поддерживает глобальный wildcard `*`. Не обходит HSTS. Связан с `HttpsOnlyMode` и `HttpsUpgradesEnabled` |
| 🟡 `NetworkPredictionOptions` | DNS prefetch, TCP preconnect, prerender страниц | `0` = всегда, `1` = устарел (= 0), `2` = никогда | `2` | Значение `1` удалено с Chrome 52 — использовать только `0` или `2` |
| 🟡 `IPv6ReachabilityOverrideEnabled` | Принудительные AAAA-запросы при резолвинге | `true` = всегда, `false` = только при IPv6-доступности | `not set` | Менять только при диагностированных проблемах с IPv6-резолвингом |
| 🟡 `DomainReliabilityAllowed` | Отправка телеметрии надёжности соединений в Google | `true` = разрешить, `false` = запретить | `false` | По умолчанию следует за `MetricsReportingEnabled`. Требует перезапуска |

---

## 5. Safe Browsing

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🔴 `SafeBrowsingProtectionLevel` | Уровень защиты Safe Browsing | `0` = отключён, `1` = стандартный (хеши URL), `2` = расширенный (полные URL → Google) | `1` | Заменяет устаревший `SafeBrowsingEnabled`. Уровень `2` передаёт полные URL и данные браузинга в Google |
| 🔴 `SafeBrowsingExtendedReportingEnabled` | Расширенная отчётность: системная инфо + контент страниц → Google | `true` = включить, `false` = выключить | `false` | Дополнение к Safe Browsing — передаёт значительно больше данных. Связан с `SafeBrowsingProtectionLevel` |
| ⛔ `SafeBrowsingEnabled` | Устаревшая политика Safe Browsing | `true`, `false` | **Не использовать** | Deprecated с Chrome 83. Игнорируется при наличии `SafeBrowsingProtectionLevel` |

---

## 6. Загрузки

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🟡 `DownloadRestrictions` | Ограничения на загрузку файлов через Safe Browsing | `0` = нет, `1` = блок. вредоносных + опасных типов, `2` = + нежелательных, `3` = всех, `4` = только вредоносных | `4` | Значения `1` и `2` дают больше ложных срабатываний. Значение `4` — официально рекомендуемое Google |

---

## 7. Телеметрия и фидбек

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🔴 `MetricsReportingEnabled` | Анонимная телеметрия использования и краш-репорты → Google | `true` = включить, `false` = выключить | `false` | Применяется на уровне browser. Требует перезапуска. Автоматически отключает `FeedbackSurveysEnabled` |
| 🔴 `UrlKeyedAnonymizedDataCollectionEnabled` | Отправка URL посещаемых страниц в Google | `true` = включить, `false` = выключить | `false` | Несмотря на "anonymized" — передаёт реальные URL всех страниц |
| 🔴 `UserFeedbackAllowed` | Отправка фидбека (скриншот + URL + системная инфо) в Google | `true` = разрешить, `false` = запретить | `false` | Непредсказуемый канал утечки данных от пользователей |
| 🟡 `FeedbackSurveysEnabled` | Встроенные опросы Google в браузере | `true` = включить, `false` = выключить | `false` | Автоматически отключается при `MetricsReportingEnabled = false`. Связан с `MetricsReportingEnabled` |
| 🟡 `UrlKeyedMetricsAllowed` | Сбор метрик с привязкой к URL | `true` = разрешить, `false` = запретить | `false` (предположительно) | ⚠️ Документация не была предоставлена. Рекомендуется `false` по аналогии с `UrlKeyedAnonymizedDataCollectionEnabled` |

---

## 8. Куки и кэш

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🔴 `BlockThirdPartyCookies` | Блокировка сторонних cookie (cross-site tracking) | `true` = блокировать, `false` = разрешить | `true` | ⚠️ Может сломать SAML/OAuth SSO. Тестировать перед внедрением. Исключения — через `CookiesAllowedForUrls` |
| 🟡 `CacheEncryptionEnabled` | Шифрование HTTP-кэша на диске | `true` = шифровать, `false` = не шифровать | `true` | Доступна с Chrome 146. Небольшое снижение производительности |
| ⚠️ `DiskCacheDir` | Директория дискового кэша | Строка пути | Настроить при необходимости | Не указывать корневой раздел. Только при конкретном сценарии (RAM-диск, DLP-агент и т.д.) |

---

## 9. Геолокация

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🔴 `DefaultGeolocationSetting` | Доступ сайтов к физическому местоположению по умолчанию | `1` = разрешить, `2` = запретить, `3` = спрашивать | `2` | Связан с `PreciseGeolocationAllowedForUrls` |
| 🔴 `PreciseGeolocationAllowedForUrls` | Whitelist для точной геолокации без запроса разрешения | Список URL-паттернов | Не задавать (пустой) | Обходит `DefaultGeolocationSetting = 2` — каждый URL в списке получает геолокацию без запроса |

---

## 10. Захват экрана, камера и аудио

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🔴 `ScreenCaptureAllowed` | Разрешение сайтам захватывать экран | `true` = разрешить, `false` = запретить | `false` | ⚠️ Сломает демонстрацию экрана в Meet/Zoom/Teams. Добавь домены в `ScreenCaptureAllowedByOrigins` |
| 🔴 `AudioCaptureAllowed` | Доступ сайтов к микрофону | `true` = разрешить, `false` = запретить | `false` | ⚠️ Сломает голосовые звонки. Whitelist через `AudioCaptureAllowedUrls`. Связан с `VideoCaptureAllowed` |
| 🔴 `VideoCaptureAllowed` | Доступ сайтов к камере | `true` = разрешить, `false` = запретить | `false` | ⚠️ Сломает видеозвонки. Whitelist через `VideoCaptureAllowedUrls`. Связан с `AudioCaptureAllowed` |
| 🟡 `TabCaptureAllowedByOrigins` | Whitelist для захвата вкладок | Список URL-паттернов | Не задавать (пустой) | Обходит `ScreenCaptureAllowed = false` — приоритет выше. Связан с `ScreenCaptureAllowed` |

---

## 11. Google-интеграции — отключить

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🔴 `TranslateEnabled` | Встроенный переводчик Chrome (контент страниц → Google Translate) | `true` = включить, `false` = выключить | `false` | Полное содержимое страницы уходит в Google Translate |
| 🔴 `EnableMediaRouter` | Google Cast (трансляция на Chromecast/TV) | `true` = включить, `false` = выключить | `false` | Требует перезапуска браузера. Взаимодействует с облачной инфраструктурой Google |
| 🔴 `GoogleSearchSidePanelEnabled` | Боковая панель Google Search на любой странице | `true` = включить, `false` = выключить | `false` | Связан с `AIModeSettings` — оба отключают Google AI точки входа |
| 🔴 `SideSearchEnabled` | Результаты поиска в боковой панели браузера | `true` = включить, `false` = выключить | `false` | Требует перезапуска браузера |
| 🔴 `ShoppingListEnabled` | Отслеживание цен товаров через Google Shopping API | `true` = включить, `false` = выключить | `false` | Передаёт URL страниц товаров в Google Shopping |
| 🔴 `MediaRecommendationsEnabled` | Персонализированные медиарекомендации Chrome | `true` = включить, `false` = выключить | `false` | Персонализация требует сбора данных о просматриваемом контенте |
| 🔴 `DesktopSharingHubEnabled` | Хаб шаринга страниц в адресной строке и меню | `true` = включить, `false` = выключить | `false` | Точка доступа к шарингу через QR, Cast, Bluetooth и другие сервисы |
| 🔴 `TabGroupSharingSettings` | Совместная работа с группами вкладок через Google | `0` = разрешить, `1` = запретить | `1` | Только через Google Admin Console. Передаёт URL и состояние вкладок в облако |

---

## 12. Приватность браузера

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🔴 `LiveTranslateEnabled` | Перевод субтитров через серверы Google | `true` = включить, `false` = выключить | `false` | Явная передача субтитров в Google. Автоотключается при `LiveCaptionEnabled = false`. Связан с `LiveCaptionEnabled` |
| 🟡 `LiveCaptionEnabled` | Живые субтитры (локальная обработка) | `true` = включить, `false` = выключить | `false` | Обработка локальная, но загрузка языковой модели от Google. Отключает `LiveTranslateEnabled`. Связан с `LiveTranslateEnabled` |
| 🔴 `PasswordSharingEnabled` | Шаринг паролей через Google Family Service | `true` = включить, `false` = выключить | `false` | Пароли передаются через облако Google. Связан с `SyncDisabled` |
| 🔴 `PasswordLeakDetectionEnabled` | Проверка паролей по базе утечек (хеш → Google) | `true` = включить, `false` = выключить | `false` | Передаёт хеш пары username+password в Google |
| 🔴 `PromotionsEnabled` | Промо-страницы Chrome (вход в аккаунт, дефолтный браузер) | `true` = включить, `false` = выключить | `false` | Подталкивает пользователей к синхронизации с Google-аккаунтом. Заменяет устаревший `PromotionalTabsEnabled` |
| 🟡 `ScrollToTextFragmentEnabled` | Скролл к тексту по URL-фрагменту (#:~:text=...) | `true` = включить, `false` = выключить | `false` | Timing attack: можно выяснить наличие текста на приватной странице. Требует перезапуска |
| 🔴 `BackgroundModeEnabled` | Chrome работает в фоне после закрытия всех окон | `true` = включить, `false` = выключить | `false` | Расширения продолжают работать и отправлять запросы. Применяется на уровне browser |

---

## 13. Безопасность устройства и браузера

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🔴 `RemoteDebuggingAllowed` | Удалённая отладка через Chrome DevTools Protocol | `true` = разрешить, `false` = запретить | `false` | CDP даёт полный доступ к браузеру: cookies, DOM, JS, перехват запросов. Требует перезапуска |
| 🔴 `EnableExperimentalPolicies` | Применение нестабильных экспериментальных политик | Список строк | Не задавать | Google предупреждает о возможной потере данных браузера |
| 🟡 `URLBlocklist` | Блокировка доступа к URL на уровне браузера | Список URL-паттернов | Настроить под контекст | Минимум: `accounts.google.com`. С Chrome 147 `*` не блокирует `chrome://`. Связан с `URLAllowlist` |

---

## 14. Производительность

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🟢 `HighEfficiencyModeEnabled` | Режим экономии памяти (Memory Saver) | `true` = включить, `false` = выключить | `true` | Применяется на уровне browser. Обязательная пара для `MemorySaverModeSavings`. Связан с `MemorySaverModeSavings` |
| 🟢 `MemorySaverModeSavings` | Агрессивность выгрузки неактивных вкладок | `0` = умеренная, `1` = сбалансированная, `2` = максимальная | `1` | Требует `HighEfficiencyModeEnabled = true`. Значение `2` вызывает частые перезагрузки вкладок. Связан с `HighEfficiencyModeEnabled` |

---

## 15. Печать

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| 🟢 `PrintingEnabled` | Разрешение печати из браузера | `true` = включить, `false` = выключить | `true` | Нет передачи данных. Отключать только при специальном требовании |
| 🟡 `SilentPrintingEnabled` | Мгновенная печать без диалога подтверждения | `true` = включить, `false` = выключить | `false` | Риск случайной или злонамеренной печати конф. документов. Chrome 144+. Применяется на уровне browser |

---

## 16. Конфигурируемые (зависят от инфраструктуры)

| Политика | Краткое описание | Доступные значения | Рекомендуемое значение | Примечание |
|---|---|---|---|---|
| ⚠️ `SiteSearchSettings` | Корпоративные ярлыки поиска в адресной строке | JSON (name, shortcut, url) | Настроить под корп. системы | Нет передачи данных — только конфигурация шаблонов URL. Требует AD-домен / MDM / Chrome Enterprise |
| ⚠️ `ForceYouTubeRestrict` | Принудительный Restricted Mode на YouTube | `0` = нет, `1` = Moderate, `2` = Strict | По политике компании | Нейтральное с точки зрения приватности. Только для контент-фильтрации. Не работает в Android-приложении YouTube |
| ⚠️ `ChromeDataRegionSetting` | Регион хранения корпоративных данных на серверах Google | `0` = любой, `1` = США, `2` = ЕС | Не актуально | При максимальной изоляции данные в Google не передаются. Только Google Admin Console |
| ⚠️ `DefaultGeolocationSetting` | см. раздел «Геолокация» | | `2` | |

---

## 17. Устаревшие политики (не использовать)

| Политика | Краткое описание | Заменена на | Примечание |
|---|---|---|---|
| ⛔ `SafeBrowsingEnabled` | Deprecated с Chrome 83 | `SafeBrowsingProtectionLevel` | Игнорируется при наличии `SafeBrowsingProtectionLevel` |
| ⛔ `PromotionalTabsEnabled` | Deprecated | `PromotionsEnabled` | Не использовать |
| ⛔ `ForceSafeSearch` | Deprecated | `ForceGoogleSafeSearch` + `ForceYouTubeRestrict` | Игнорируется при наличии новых политик |

---

## Сводная таблица приоритетов

### 🔴 Критические (внедрить первыми)

```
BrowserSignin                          = 0
SyncDisabled                           = true
GenAiDefaultSettings                   = 2
GeminiSettings                         = 1
AIModeSettings                         = 1
CloudReportingEnabled                  = false
UserSecuritySignalsReporting           = false
MetricsReportingEnabled                = false
UrlKeyedAnonymizedDataCollectionEnabled = false
DnsOverHttpsMode                       = secure
DnsOverHttpsTemplates                  = https://dns.quad9.net/dns-query
WebRtcIPHandling                       = disable_non_proxied_udp
HttpsOnlyMode                          = force_enabled
RemoteDebuggingAllowed                 = false
BlockThirdPartyCookies                 = true
```

### 🟡 Высокий приоритет

```
SafeBrowsingProtectionLevel            = 1
SafeBrowsingExtendedReportingEnabled   = false
ScreenCaptureAllowed                   = false
AudioCaptureAllowed                    = false
VideoCaptureAllowed                    = false
DefaultGeolocationSetting              = 2
BackgroundModeEnabled                  = false
SearchContentSharingSettings           = 1
TabGroupSharingSettings                = 1
TranslateEnabled                       = false
PasswordLeakDetectionEnabled           = false
UserFeedbackAllowed                    = false
DomainReliabilityAllowed               = false
CacheEncryptionEnabled                 = true
```

### 🟢 Стандартный приоритет

```
HighEfficiencyModeEnabled              = true
MemorySaverModeSavings                 = 1
QuicAllowed                            = true
HttpsUpgradesEnabled                   = true
NetworkPredictionOptions               = 2
PrintingEnabled                        = true
SilentPrintingEnabled                  = false
```

---

*Составлено на основе официальной документации Google Chrome Enterprise Policies.*  
*Рекомендации применяются в контексте максимальной приватности — никаких данных в Google.*
