# Analytical Roadmap — Security, Compliance & Governance (MorNet / GreenCore)

**Версия:** 0.2 (аналитическая, с матрицей стандартов)
**Дата:** 15.10.2025
**Период:** октябрь 2025 – январь 2026
**Назначение:** сбор требований и лучших практик для будущего внедрения безопасности, комплаенса, ESG-аудита и DAO-механизмов, без реализации кода.

---

## 1. Безопасность и киберустойчивость (Security Baseline)

### Цель
Создать аналитическую основу для будущего внедрения безопасной архитектуры и DevSecOps-контуров.

### Задачи
- Анализ нормативов: **ФЗ-187, ФСТЭК, ФСБ, ISO/IEC 27001, 62443, OWASP ASVS**.
- Матрица критичности компонентов системы.
- Модель безопасности: Zero Trust / mTLS / PKI / HSM / RBAC.
- Рекомендованные инструменты: Vault, HashiCorp Boundary, GitLab Secret Scanning, cert-rotation.
- Требования к логированию и WORM-аудиту (≥ 7 лет хранения).
- Минимальный baseline для DevSecOps: SCA, SAST, dependency audit.

**Выход:** `security_baseline.md` — аналитика, матрица рисков, перечень инструментов и стандартов.

---

## 2. Комплаенс и правовой контур (GC-Guard Requirements)

### Цель
Определить все юридические и регуляторные требования к DAO Treasury, токенам, пользователям и обменным операциям.

### Задачи
- Нормативы РФ: **ФЗ-259, ФЗ-115, ФЗ-152, ПП-719**, ISO/ESG стандарты.
- Международные: FATF Travel Rule, ISO 20022, AMLD6.
- Матрица ролей KYC/KYB и лимитов (ФЗ-115).
- Логика AML/MRV, типы отчётности и лимиты транзакций.
- Требования сертификации (СКЗИ, ОКВЭД, ISO-серии).
- Политики пост-MVP: AML/KYC, MRV/ESG, Mint/Freeze, Data Retention.

**Выход:** `compliance_gcguard.md` — матрица требований и дорожная карта комплаенса.

---

## 3. Банк и платежный шлюз (FinTech & CBDC Integration)

### Цель
Подготовить требования к будущей интеграции с банками и цифровыми валютами (CBDC).

### Задачи
- Изучить API ISO 20022 (pain.001, camt.053, pacs.008 и др.).
- Список банков РФ и BRICS+ с sandbox API.
- AML-лимиты и регламент транзакций для физ/юр лиц.
- Анализ возможностей интеграции с Цифровым рублём и цифровым юанем.
- Схема архитектуры шлюза: auth → escrow → callback API.

**Выход:** `bank_integration_brief.md` — таблица API и диаграмма взаимодействия.

---

## 4. ESG и MRV-аудит (экология и устойчивое развитие)

### Цель
Разработать модель оценки углеродного и энергетического следа для каждого токена.

### Задачи
- Методика MRV по **ISO 14064-1, GHG Protocol**.
- ESG-метрики: Energy Efficiency, Carbon Footprint, Reuse Ratio.
- Данные для расчёта углеродных кредитов (CRB Token).
- Концепция верификации через DAO (оценка достоверности данных).

**Выход:** `esg_mrv_model.md` — описание методики, источников и ESG-метрик.

---

## 5. Edge-инфраструктура и IoT (без внедрения)

### Цель
Определить стандарты и протоколы взаимодействия, которые будут поддержаны в будущем (Modbus, OPC-UA, MQTT, LwM2M).

### Задачи
- Перечень протоколов и форматов данных.
- Модель буферизации (offline store & forward).
- Требования безопасности обмена: TLS, device-cert, mTLS.
- Таблица «Тип устройства → частота → метод подписи».

**Выход:** `edge_data_exchange.md` — спецификация протоколов и метаданных телеметрии.

---

## 6. DevSecOps и управление средами

### Цель
Собрать лучшие практики CI/CD для критичных инфраструктур.

### Задачи
- Изучить **CIS Benchmarks**, GitLab Secure Pipelines, OWASP DevSecOps.
- Матрица окружений (Dev, Stage, Prod, Pilot) + разделение доступов.
- План внедрения SAST/DAST/SCA.
- Регламент работы с секретами (Vault/Protected Vars).

**Выход:** `devsecops_guideline.md` — аналитика процессов, чек-лист требований.

---

## 7. Governance и DAO-механизмы

### Цель
Собрать лучшие практики децентрализованного управления (DAO Treasury, голосование, индексирование решений).

### Задачи
- Анализ протоколов: **Aragon, Snapshot, Compound Governor, Gnosis Safe**.
- Определить типы голосований (simple, quadratic, delegated).
- Составить roadmap внедрения DAO после MVP.

**Выход:** `dao_governance_brief.md` — аналитика DAO-механизмов и план внедрения.

---

## 8. Формат и структура хранения

```
/docs/analytics/
 ├─ security_baseline.md
 ├─ compliance_gcguard.md
 ├─ bank_integration_brief.md
 ├─ esg_mrv_model.md
 ├─ edge_data_exchange.md
 ├─ devsecops_guideline.md
 └─ dao_governance_brief.md
```

Все документы ведутся в Markdown-формате, имеют единые метаданные (`version`, `author`, `status`), включают ссылки на нормативы и используемые стандарты.

---

## 9. Матрица соответствия стандартов и модулей MorNet / GreenCore

| Стандарт / Регламент | Описание | Затрагиваемые модули | Уровень приоритета |
|------------------------|-----------|----------------------|--------------------|
| **ISO/IEC 27001** | Управление информационной безопасностью | GC-Guard, API Gateway, DAO Treasury | 🔹 High |
| **ISO/IEC 62443** | Безопасность промышленных систем (SCADA, Edge) | Edge Agent, Token Engine | ⚙️ Medium |
| **OWASP ASVS / DevSecOps** | Безопасная разработка и тестирование | Все BE/FE модули, DevOps | 🔹 High |
| **ФЗ-115 / AMLD6 / FATF** | Борьба с отмыванием средств, KYC/KYB | GC-Guard, Wallet Service, Exchange Router | 🔹 High |
| **ISO 14064-1 / GHG Protocol** | Методика учёта углеродных выбросов | ESG/MRV Engine, Token Engine | ⚙️ Medium |
| **ФЗ-187 / КИИ РФ** | Требования к критическим ИС | GC-Guard, API Gateway, Treasury | 🔸 Medium |
| **ISO 20022 / ПП-719** | Финансовые сообщения и структура API банка | Bank/CBDC Gateway, Exchange Router | 🔹 High |
| **CIS Benchmarks** | Настройка безопасных окружений | DevOps, Infra, Kubernetes | ⚙️ Medium |
| **Aragon / Snapshot / Gnosis** | DAO-механизмы управления | DAO Treasury, Governance Layer | ⚙️ Medium |
| **ISO/ESG SR10** | Социальная ответственность и отчётность | ESG Layer, Public UI | 🔸 Medium |

---

## 10. Дорожная карта (Q4 2025 – Q1 2026)

| Период | Активность | Ответственные | Результат |
|---------|-------------|----------------|------------|
| **16–31 окт 2025** | Анализ стандартов безопасности и комплаенса, сбор нормативных актов | Security Lead / Compliance Lead | Черновики `security_baseline.md`, `compliance_gcguard.md` |
| **1–15 ноя 2025** | Аналитика банковских API и CBDC, структура GC-Guard KYC матрицы | FinTech Lead / Legal Advisor | `bank_integration_brief.md`, KYC-матрица |
| **16–30 ноя 2025** | Разработка MRV-модели и ESG-метрик, сбор методик ISO 14064-1 | ESG Lead / Data Analyst | `esg_mrv_model.md` |
| **1–15 дек 2025** | Edge / IoT стандарты и DevSecOps baseline | IoT Lead / DevOps Lead | `edge_data_exchange.md`, `devsecops_guideline.md` |
| **16–31 дек 2025** | Аналитика DAO механизмов, подготовка governance-модели | Blockchain Lead / Product Owner | `dao_governance_brief.md` |
| **янв 2026** | Финализация аналитических отчётов, интеграция с roadmap MVP | CTO / PMO | Единый архив `/docs/analytics/` |

---

## 11. Результат

Команда получает **комплекс аналитических спецификаций**, определяющих будущие требования к безопасности, комплаенсу, ESG, банку и DAO. Все эти документы:

- Не требуют реализации на этапе MVP.
- Готовят нормативно-методическую основу для post-MVP.
- Позволяют интегрировать стандарты и практики без переработки архитектуры.

**Финальный артефакт:** архив `/docs/analytics` с семью аналитическими файлами и таблицей соответствия стандартов и модулей MorNet / GreenCore.

