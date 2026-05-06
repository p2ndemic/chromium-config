# Chrome Group Policy — Сводная таблица рекомендаций
**Контекст:** Максимальная приватность (нет передачи данных в Google) + Безопасность + Производительность  
**Дата анализа:** Май 2026

---

## Условные обозначения
| Метка | Значение |
|---|---|
| 🔴 Критично | Высокий приоритет, влияет на безопасность или утечку данных |
| 🟡 Важно | Средний приоритет, рекомендуется к настройке |
| 🟢 Настройка | Конфигурируется под нужды организации |
| ⚫ Deprecated | Политика устарела, не использовать |

---

## 1. Cloud Reporting

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `CloudReportingEnabled` 🔴 | Основной канал отправки данных о браузере в Google Admin Console | `true` / `false` | `false` | Зависимость для `CloudExtensionRequestEnabled`, `CloudReportingUploadFrequency`. Применяется на уровне browser (machine GPO) |
| `CloudExtensionRequestEnabled` 🔴 | Отправка запросов на установку расширений в Google Admin Console | `true` / `false` | `false` | Не работает без `CloudReportingEnabled = true`. При отключённом Cloud Reporting — автоматически неактивна |
| `CloudProfileReportingEnabled` 🔴 | Отправка данных профиля (версия ОС, расширения, политики) в Google Admin Console | `true` / `false` | `false` | Только cloud user policy. Часть группы Cloud Reporting |
| `CloudReportingUploadFrequency` 🟡 | Частота отправки отчётов в Google Admin Console (в часах) | `3` – `24` | Не актуально | Не имеет смысла при `CloudReportingEnabled = false` |
| `UserSecuritySignalsReporting` 🔴 | Отправка сигналов безопасности (версия ОС, расширения, политики) на серверы Google | `true` / `false` | `false` | Только cloud user policy. Зависит от `UserSecurityAuthenticatedReporting` |
| `UserSecurityAuthenticatedReporting` 🔴 | First-party аутентификация при отправке security signals | `true` / `false` | `false` | Не работает без `UserSecuritySignalsReporting = true` |
| `ReportVersionData` 🔴 | Отправка данных о версии браузера | `true` / `false` | `false` | Гранулярный контроль в рамках Cloud Reporting. Defence-in-depth при `CloudReportingEnabled = false` |
| `ReportPolicyData` 🔴 | Отправка данных о применённых политиках | `true` / `false` | `false` | Гранулярный контроль в рамках Cloud Reporting |
| `ReportMachineIDData` 🔴 | Отправка идентификатора машины | `true` / `false` | `false` | Гранулярный контроль в рамках Cloud Reporting |
| `ReportUserIDData` 🔴 | Отправка идентификатора пользователя | `true` / `false` | `false` | Гранулярный контроль в рамках Cloud Reporting |
| `ReportExtensionsAndPluginsData` 🔴 | Отправка данных об установленных расширениях | `true` / `false` | `false` | Гранулярный контроль в рамках Cloud Reporting |
| `ReportSafeBrowsingData` 🔴 | Отправка данных Safe Browsing | `true` / `false` | `false` | Гранулярный контроль в рамках Cloud Reporting |

---

## 2. Google AI и Generative AI

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `GenAiDefaultSettings` 🔴 | **Мастер-рубильник:** задаёт дефолт для всех AI-функций Chrome, не настроенных индивидуально | `0` = разрешить + улучшение моделей / `1` = разрешить без улучшения / `2` = запретить всё | `2` | Только Google Admin Console. Автоматически отключает все неявно настроенные AI-функции, включая будущие. Индивидуальные политики имеют приоритет |
| `GeminiSettings` 🔴 | Интеграция Gemini с браузером | `0` = разрешить / `1` = запретить | `1` | При `GenAiDefaultSettings = 2` отключается автоматически. Явное значение — defence-in-depth |
| `AIModeSettings` 🔴 | AI Mode в адресной строке и на странице новой вкладки | `0` = разрешить / `1` = запретить | `1` | При `GenAiDefaultSettings = 2` отключается автоматически |
| `AutofillPredictionSettings` 🔴 | GenAI для автозаполнения форм (данные форм → Google) | `0` = разрешить + улучшение / `1` = разрешить без улучшения / `2` = запретить | `2` | При `GenAiDefaultSettings = 2` отключается автоматически |
| `DevToolsGenAiSettings` 🔴 | AI в Chrome DevTools: консоль, стек-трейсы, сетевые запросы → Google | `0` = разрешить + улучшение / `1` = разрешить без улучшения / `2` = запретить | `2` | Передаёт ошибки, код, запросы. Response body и куки — не передаются |
| `HistorySearchSettings` 🔴 | AI-поиск по истории браузера (содержимое страниц → Google) | `0` = разрешить + улучшение / `1` = разрешить без улучшения / `2` = запретить | `2` | Передаёт не только URL, но и содержимое страниц. При `GenAiDefaultSettings = 2` отключается автоматически |
| `SearchContentSharingSettings` 🔴 | Шаринг содержимого страниц с Google AI Mode и Lens через боковую панель | `0` = разрешить / `1` = запретить | `1` | Независим от `AIModeSettings`. Отключить обе политики необходимо |
| `GoogleSearchSidePanelEnabled` 🔴 | Встроенная боковая панель поиска Google на любой странице | `true` / `false` | `false` | Передаёт контекст страницы и запросы в Google |

---

## 3. Синхронизация и аккаунт Google

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `BrowserSignin` 🔴 | Управление входом в браузер с Google-аккаунтом | `0` = запретить / `1` = разрешить / `2` = принудить | `0` | Применяется на уровне browser. Требует перезапуска. Пользователи смогут входить в веб-сервисы Google (Gmail), но не в браузер |
| `SyncDisabled` 🔴 | Отключение синхронизации данных с Google (история, пароли, закладки, вкладки, расширения) | `true` = отключить / `false` = разрешить | `true` | Работает в паре с `BrowserSignin = 0`. Конфликтует с `RoamingProfileSupportEnabled` — не включать одновременно |
| `SharedClipboardEnabled` 🔴 | Шаринг буфера обмена между устройствами через Google Sync | `true` / `false` | `false` | Требует `BrowserSignin` и Sync — уже отключены. Явное значение для defence-in-depth |
| `ProfileSeparationSettings` 🟡 | Поведение браузера при входе в управляемый аккаунт | `0` = предложить / `1` = принудить / `2` = отключить | `2` | Только Google Admin Console, только profile-level. Не работает при `BrowserSignin = 0`, но исключает всплывающие окна |
| `PasswordSharingEnabled` 🔴 | Передача паролей другим пользователям через Family Service Google | `true` / `false` | `false` | Пароли уходят в облако Google через Family Service. Управляемые аккаунты не могут создавать семейные группы, но политику фиксируем явно |

---

## 4. Телеметрия и метрики

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `MetricsReportingEnabled` 🔴 | Анонимная телеметрия использования и краш-репорты → Google | `true` / `false` / not set | `false` | Применяется на уровне browser. Требует перезапуска. Автоматически отключает `FeedbackSurveysEnabled` |
| `UrlKeyedAnonymizedDataCollectionEnabled` 🔴 | Отправка URL посещаемых страниц в Google | `true` / `false` / not set | `false` | "Anonymized" не означает, что Google не видит URL — только отсутствие привязки к аккаунту |
| `FeedbackSurveysEnabled` 🔴 | Встроенные опросы Google в браузере | `true` / `false` | `false` | Автоматически отключается при `MetricsReportingEnabled = false` |
| `DomainReliabilityAllowed` 🔴 | Диагностические данные о сбоях DNS и HTTP-ошибках → Google | `true` / `false` | `false` | Следует за `MetricsReportingEnabled` если не задан явно. Требует перезапуска. Применяется на уровне browser |
| `UserFeedbackAllowed` 🔴 | Отправка отчётов об ошибках (скриншот + URL + системная информация) → Google | `true` / `false` | `false` | Канал непредсказуемой утечки данных в корпоративной среде |
| `UrlKeyedMetricsAllowed` 🔴 | Сбор метрик с привязкой к URL | `true` / `false` (предположительно) | `false` | Документация не предоставлена. По аналогии с `UrlKeyedAnonymizedDataCollectionEnabled` — отключить |

---

## 5. Safe Browsing

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `SafeBrowsingProtectionLevel` 🟡 | Уровень защиты Safe Browsing | `0` = выкл / `1` = standard / `2` = enhanced | `1` | Enhanced (2) передаёт полные URL в Google. Standard (1) — только хеши. Заменяет устаревшую `SafeBrowsingEnabled` |
| `SafeBrowsingExtendedReportingEnabled` 🔴 | Расширенная отчётность: системная информация и содержимое страниц → Google | `true` / `false` / not set | `false` | Значительно шире базового Safe Browsing. Обязательно отключить |
| `SafeBrowsingEnabled` ⚫ | **DEPRECATED с Chrome 83** | `true` / `false` / not set | Не использовать | Игнорируется при наличии `SafeBrowsingProtectionLevel`. Заменить на актуальную политику |

---

## 6. Сеть и DNS

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `DnsOverHttpsMode` 🔴 | Режим DNS-over-HTTPS | `off` / `automatic` / `secure` | `secure` | Обязательно настраивать в паре с `DnsOverHttpsTemplates`. Без шаблона при `secure` — DNS резолвинг упадёт |
| `DnsOverHttpsTemplates` 🔴 | URI-шаблон DoH-резолвера | Строка URI | Явно задать | При `secure` — обязателен. Рекомендуется: Quad9 (`https://dns.quad9.net/dns-query`) или корпоративный резолвер. Зависит от `DnsOverHttpsMode` |
| `WebRtcIPHandling` 🔴 | Контроль IP-адресов, используемых WebRTC (защита от WebRTC IP leak) | `default` / `default_public_and_private_interfaces` / `default_public_interface_only` / `disable_non_proxied_udp` | `disable_non_proxied_udp` | Максимальная защита от утечки IP. Компромисс: может ухудшить качество WebRTC-звонков. Альтернатива — `default_public_interface_only` |
| `QuicAllowed` 🟢 | Разрешение протокола QUIC (HTTP/3) | `true` / `false` | `true` (или not set) | QUIC — транспортный протокол, не канал передачи данных. Отключение снижает производительность без выигрыша в приватности. Требует перезапуска |
| `NetworkPredictionOptions` 🟡 | DNS prefetch, TCP preconnect, prerender страниц | `0` = всегда / `2` = никогда | `2` | Значение 1 удалено с Chrome 52 и обрабатывается как 0. Предзагрузка создаёт сетевые запросы к непосещённым URL |
| `IPv6ReachabilityOverrideEnabled` 🟢 | Принудительные AAAA-запросы при DNS-резолвинге | `true` / `false` | not set | Менять только при диагностированных проблемах с IPv6. Значение `true` может замедлить DNS в сетях без IPv6 |
| `HttpAllowlist` 🟢 | Список хостов, исключённых из HTTPS-принудиловки | Список hostname-паттернов | Настроить явно | Только реально необходимые HTTP-хосты. Wildcard `*` или `[*]` запрещены. Не применяется к HSTS. Связана с `HttpsOnlyMode` и `HttpsUpgradesEnabled` |

---

## 7. HTTPS и шифрование соединений

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `HttpsOnlyMode` 🔴 | Принудительный апгрейд всех соединений до HTTPS | `allowed` / `disallowed` / `force_enabled` / `force_balanced_enabled` | `force_enabled` | Внутренние HTTP-ресурсы добавлять в `HttpAllowlist`. Связана с `HttpsUpgradesEnabled` |
| `HttpsUpgradesEnabled` 🟡 | Автоматический апгрейд HTTP-ссылок до HTTPS | `true` / `false` / not set | `true` | Дополняет `HttpsOnlyMode`. Исключения — через `HttpAllowlist` |
| `CacheEncryptionEnabled` 🔴 | Шифрование HTTP-кэша браузера на диске | `true` / `false` | `true` | Доступна с Chrome 146. На более старых версиях игнорируется. Небольшое снижение производительности чтения кэша |

---

## 8. Геолокация

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `DefaultGeolocationSetting` 🔴 | Доступ сайтов к физическому местоположению пользователя | `1` = разрешить / `2` = запретить / `3` = спрашивать | `2` | Исключения для корпоративных инструментов — через отдельный whitelist |
| `PreciseGeolocationAllowedForUrls` 🔴 | Whitelist сайтов с доступом к точной геолокации без запроса | Список URL-паттернов | Не задавать (пустой список) | Любой URL в этом списке обходит `DefaultGeolocationSetting = 2`. Хранить пустым |

---

## 9. Захват экрана, камеры и аудио

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `ScreenCaptureAllowed` 🔴 | Разрешение сайтам запрашивать захват экрана | `true` / `false` | `false` | Исключения — через `ScreenCaptureAllowedByOrigins`. Для видеозвонков добавить домены (Meet, Zoom, Teams) |
| `AudioCaptureAllowed` 🔴 | Разрешение сайтам запрашивать доступ к микрофону | `true` / `false` | `false` | Исключения — через `AudioCaptureAllowedUrls`. Связана с `ScreenCaptureAllowed` |
| `VideoCaptureAllowed` 🔴 | Разрешение сайтам запрашивать доступ к камере | `true` / `false` | `false` | Исключения — через `VideoCaptureAllowedUrls`. Влияет на все видеовходы, включая внешние камеры |
| `TabCaptureAllowedByOrigins` 🔴 | Whitelist сайтов для захвата вкладок | Список URL-паттернов | Не задавать | Обходит `ScreenCaptureAllowed = false` — опасно. Приоритет выше `ScreenCaptureAllowed` и `ScreenCaptureAllowedByOrigins` |

---

## 10. Шаринг и буфер обмена

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `TabGroupSharingSettings` 🔴 | Совместная работа с группами вкладок через облако Google | `0` = разрешить / `1` = запретить | `1` | Только Google Admin Console. Данные об открытых вкладках уходят в облако |
| `DesktopSharingHubEnabled` 🟡 | Кнопка "Поделиться" в адресной строке и меню | `true` / `false` | `false` | Убирает иконку шаринга из омнибокса. Связан с `EnableMediaRouter` |
| `EnableMediaRouter` 🟡 | Google Cast — трансляция на Chromecast/Smart TV | `true` / `false` | `false` | Требует перезапуска. Взаимодействует с облачной инфраструктурой Google для обнаружения устройств |

---

## 11. Живые субтитры и перевод

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `LiveCaptionEnabled` 🟡 | Живые субтитры (локальная обработка) | `true` / `false` / not set | `false` | Модель скачивается при первом включении. Зависимость для `LiveTranslateEnabled` |
| `LiveTranslateEnabled` 🔴 | Перевод живых субтитров — содержимое явно передаётся в Google | `true` / `false` / not set | `false` | Автоматически отключается при `LiveCaptionEnabled = false`. Явное значение — defence-in-depth |

---

## 12. Производительность памяти

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `HighEfficiencyModeEnabled` 🟡 | Режим экономии памяти (выгрузка неактивных вкладок) | `true` / `false` / not set | `true` | Применяется на уровне browser. Обязательная пара для `MemorySaverModeSavings` — без неё `MemorySaverModeSavings` не работает |
| `MemorySaverModeSavings` 🟡 | Уровень агрессивности Memory Saver | `0` = умеренный / `1` = balanced / `2` = максимальный | `1` | Требует `HighEfficiencyModeEnabled = true`. Значение 2 вызывает частые перезагрузки вкладок |

---

## 13. Cookies и трекинг

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `BlockThirdPartyCookies` 🔴 | Блокировка сторонних cookie (cross-site tracking) | `true` / `false` / not set | `true` | Тестировать SSO до внедрения — SAML/OAuth может сломаться. Исключения — через `CookiesAllowedForUrls`. Не применяется в режиме Инкогнито |

---

## 14. Загрузки

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `DownloadRestrictions` 🟡 | Ограничения на загрузку файлов по Safe Browsing | `0` = нет / `1` = блок вредонос + опасные типы / `2` = блок вредонос + нежелат. + опасные / `3` = всё / `4` = только вредоносные | `4` | Значения 1–2 требуют передачи метаданных в Google. Значение 4 — официальная рекомендация Google |

---

## 15. Безопасность браузера

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `RemoteDebuggingAllowed` 🔴 | Разрешение удалённой отладки через CDP (Chrome DevTools Protocol) | `true` / `false` | `false` | Применяется на уровне browser. Требует перезапуска. CDP даёт полный доступ к сессии браузера |
| `ScrollToTextFragmentEnabled` 🟡 | Скролл к тексту по URL-фрагменту (#:~:text=...) | `true` / `false` | `false` | Требует перезапуска. Уязвим к timing attack для определения наличия текста на приватной странице |
| `EnableExperimentalPolicies` 🔴 | Применение экспериментальных (нестабильных) политик | Список строк | not set | Риск потери данных браузера. Политики могут быть изменены или удалены без предупреждения |
| `PasswordLeakDetectionEnabled` 🟡 | Проверка паролей по базе утечек (хеш пары → Google) | `true` / `false` / not set | `false` | Альтернатива — корпоративный менеджер паролей с локальной проверкой |
| `PasswordSharingEnabled` 🔴 | Шаринг паролей через Google Family Service | `true` / `false` | `false` | Пароли уходят в облако. Зависит от `SyncDisabled` — уже отключён |

---

## 16. Фоновый режим

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `BackgroundModeEnabled` 🟡 | Chrome работает после закрытия всех окон | `true` / `false` / not set | `false` | Применяется на уровне browser. Расширения и push-уведомления продолжают работать в фоне незаметно для пользователя |

---

## 17. Промо и рекомендации

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `PromotionsEnabled` 🟡 | Промо-контент Chrome (приветственные страницы, вход в аккаунт Google) | `true` / `false` | `false` | Применяется на уровне browser. Заменяет устаревшую `PromotionalTabsEnabled` |
| `PromotionalTabsEnabled` ⚫ | **DEPRECATED** — промо-контент во вкладках | `true` / `false` | Не использовать | Использовать `PromotionsEnabled` вместо |
| `MediaRecommendationsEnabled` 🟡 | Персонализированные медиарекомендации (поведенческие данные → Google) | `true` / `false` | `false` | — |
| `ShoppingListEnabled` 🔴 | Отслеживание цен на товары (URL товаров → Google Shopping API) | `true` / `false` | `false` | — |
| `SideSearchEnabled` 🟡 | Результаты поиска в боковой панели | `true` / `false` | `false` | Требует перезапуска |

---

## 18. Перевод и язык

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `TranslateEnabled` 🔴 | Встроенный переводчик Chrome (содержимое страницы → Google Translate) | `true` / `false` / not set | `false` | Пользователь может непреднамеренно отправить конфиденциальный контент |

---

## 19. Печать

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `PrintingEnabled` 🟢 | Разрешение печати из браузера | `true` / `false` | `true` | Нет передачи данных. Отключать только при специальных требованиях |
| `SilentPrintingEnabled` 🟡 | Мгновенная печать без диалога подтверждения | `true` / `false` | `false` | Применяется на уровне browser. Риск случайной или злонамеренной печати конфиденциальных документов |

---

## 20. Контроль доступа к URL

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `URLBlocklist` 🟡 | Блокировка доступа к указанным URL | Список URL-паттернов | Настроить | Рекомендуется заблокировать минимум: `accounts.google.com`, `myaccount.google.com`. Не блокировать `chrome://` без проверки — вызывает ошибки |

---

## 21. Конфигурация хранилища и кэша

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `DiskCacheDir` 🟢 | Директория дискового кэша браузера | Строка пути | Опционально | Применяется на уровне browser. Требует перезапуска. Не указывать корневой раздел |
| `ChromeDataRegionSetting` 🟢 | Регион хранения данных Chrome на серверах Google | `0` = без предпочтений / `1` = США / `2` = ЕС | Не актуально | Только Google Admin Console. При максимальной изоляции данные не передаются — политика не имеет смысла |

---

## 22. Контент-фильтрация

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `ForceYouTubeRestrict` 🟢 | Принудительный Restricted Mode на YouTube | `0` = не принудительно / `1` = Moderate / `2` = Strict | По контексту | Нейтрально с точки зрения приватности. Только для контент-фильтрации. Не работает для Android-приложения YouTube |
| `ForceSafeSearch` ⚫ | **DEPRECATED** — SafeSearch в Google и YouTube | `true` / `false` | Не использовать | Игнорируется при наличии `ForceGoogleSafeSearch` или `ForceYouTubeRestrict`. Использовать актуальные политики |

---

## 23. Конфигурируемые — настраивать под организацию

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `SiteSearchSettings` 🟢 | Корпоративные поисковые ярлыки в адресной строке | JSON (name, shortcut, url) | Настроить | Данные никуда не передаются. Повышает продуктивность. Требует AD/MDM/Chrome Enterprise |
| `HttpAllowlist` 🟢 | Список хостов, исключённых из HTTPS-принудиловки | Список hostname-паттернов | Минимально необходимые | Связана с `HttpsOnlyMode` и `HttpsUpgradesEnabled` |

---

## 24. Прочие

| Политика | Описание | Доступные значения | Рекомендуемое значение | Примечания |
|---|---|---|---|---|
| `BookmarkBarEnabled` 🟢 | Отображение панели закладок | `true` / `false` / not set | not set | Нет влияния на безопасность. Оставить на выбор пользователя |
| `QuicAllowed` 🟢 | Разрешение протокола QUIC (HTTP/3) | `true` / `false` | `true` | Требует перезапуска. QUIC — протокол соединения, не канал передачи данных |

---

## Сводка по приоритетам

### 🔴 Критично — настроить в первую очередь

| Политика | Значение |
|---|---|
| `BrowserSignin` | `0` |
| `SyncDisabled` | `true` |
| `GenAiDefaultSettings` | `2` |
| `GeminiSettings` | `1` |
| `AIModeSettings` | `1` |
| `CloudReportingEnabled` | `false` |
| `MetricsReportingEnabled` | `false` |
| `UrlKeyedAnonymizedDataCollectionEnabled` | `false` |
| `DnsOverHttpsMode` + `DnsOverHttpsTemplates` | `secure` + URI |
| `WebRtcIPHandling` | `disable_non_proxied_udp` |
| `HttpsOnlyMode` | `force_enabled` |
| `RemoteDebuggingAllowed` | `false` |
| `BlockThirdPartyCookies` | `true` |
| `CacheEncryptionEnabled` | `true` |
| `ScreenCaptureAllowed` | `false` |
| `AudioCaptureAllowed` | `false` |
| `VideoCaptureAllowed` | `false` |
| `SafeBrowsingExtendedReportingEnabled` | `false` |
| `UserFeedbackAllowed` | `false` |

### 🟡 Важно — настроить после критичных

| Политика | Значение |
|---|---|
| `SafeBrowsingProtectionLevel` | `1` |
| `HighEfficiencyModeEnabled` | `true` |
| `MemorySaverModeSavings` | `1` |
| `NetworkPredictionOptions` | `2` |
| `BackgroundModeEnabled` | `false` |
| `DefaultGeolocationSetting` | `2` |
| `DownloadRestrictions` | `4` |
| `DomainReliabilityAllowed` | `false` |
| `FeedbackSurveysEnabled` | `false` |
| `EnableMediaRouter` | `false` |
| `PromotionsEnabled` | `false` |
| `TranslateEnabled` | `false` |
| `TabGroupSharingSettings` | `1` |
| `SharedClipboardEnabled` | `false` |
| `DesktopSharingHubEnabled` | `false` |
| `ShoppingListEnabled` | `false` |
| `SearchContentSharingSettings` | `1` |
| `SilentPrintingEnabled` | `false` |
| `HttpsUpgradesEnabled` | `true` |

### ⚫ Deprecated — не использовать

| Политика | Замена |
|---|---|
| `SafeBrowsingEnabled` | `SafeBrowsingProtectionLevel` |
| `PromotionalTabsEnabled` | `PromotionsEnabled` |
| `ForceSafeSearch` | `ForceGoogleSafeSearch` / `ForceYouTubeRestrict` |

---

*Документ сформирован на основе анализа документации Chrome Enterprise Group Policies*  
*Рекомендации актуальны для Chrome 146+ на платформах Windows / Linux / macOS*
