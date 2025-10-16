# Residential Sector Plugin

**Сектор:** Жилая недвижимость (домохозяйства)  
**Типы ресурсов:** E (электроэнергия), H (отопление), W (вода)  
**Статус:** 🟢 MVP 1.0 (Q1 2026)

## Описание

Residential Plugin - это sector-specific модуль для токенизации ресурсов в жилой недвижимости (домохозяйства, квартиры, частные дома).

## Поддерживаемые ресурсы

### E - Электроэнергия
- **Источники:** Wi-Fi розетки (Tapo P110, Sonoff POW R3), умные счётчики
- **Единица измерения:** кВт·ч
- **Тарифы:** дневной/ночной (T1/T2)
- **Токенизация:** 1 кВт·ч = 1 E token

### H - Отопление
- **Источники:** Газовые счётчики, тепловые счётчики
- **Единица измерения:** м³ (газ), Гкал (тепло)
- **Тарифы:** сезонные (зима/лето)
- **Токенизация:** 1 м³ газа = 0.6 H tokens

### W - Вода
- **Источники:** Водяные счётчики
- **Единица измерения:** м³
- **Тарифы:** холодная/горячая вода
- **Токенизация:** 1 м³ воды = 0.4 W tokens

## Типичные устройства

### Электроприборы:
- Холодильник (24/7, 100-200 Вт)
- Стиральная машина (1-2 цикла/день, 1-2 кВт)
- Посудомоечная машина (1 цикл/день, 1-1.5 кВт)
- Духовка (30-60 мин/день, 2-3 кВт)
- Чайник (5-10 раз/день, 2 кВт)
- Телевизор (4-8 часов/день, 100-200 Вт)
- Компьютер (8-12 часов/день, 300-500 Вт)
- Освещение (LED, 5-50 Вт на лампу)

### Отопление:
- Газовый котёл (зима: 10-20 м³/день, лето: 2-5 м³/день)
- Бойлер (нагрев воды, 2-5 м³/день)
- Радиаторы (поддержка температуры)

### Вода:
- Душ (50-100 л/день на человека)
- Туалет (6-12 л на смыв)
- Мойка посуды (10-20 л/день)
- Стиральная машина (40-60 л на цикл)

## Слотовый учёт

### Типичные слоты для домохозяйства:

| Слот | Период | Активности | Потребление |
|------|--------|------------|-------------|
| S1 | 00:00–06:00 | Сон, холодильник, отопление | Низкое |
| S2 | 06:00–09:00 | Утро: душ, завтрак, чайник | Среднее |
| S3 | 09:00–17:00 | День: холодильник, отопление | Низкое |
| S4 | 17:00–20:00 | Вечер: готовка, душ, ТВ | Высокое |
| S5 | 20:00–23:00 | Поздний вечер: ТВ, компьютер | Среднее |
| S6 | 23:00–00:00 | Подготовка ко сну | Низкое |

## Формулы токенизации

### Электроэнергия (E)
```
E_tokens = ΔE × K_time
где:
- ΔE - потребление (кВт·ч)
- K_time = 1.0 (день), 0.7 (ночь)
```

### Отопление (H)
```
H_tokens = ΔH × K_efficiency × K_season
где:
- ΔH - потребление газа (м³)
- K_efficiency = 0.6 (КПД котла)
- K_season = 1.0 (зима), 0.5 (лето)
```

### Вода (W)
```
W_tokens = ΔW × K_type
где:
- ΔW - потребление воды (м³)
- K_type = 0.4 (холодная), 0.6 (горячая)
```

## Пример: SmartHome Node 01, Slot S5

**Входные данные:**
- Электроэнергия: 2.90 кВт·ч
- Газ: 2.437 м³
- Вода: 0.09 м³

**Расчёт токенов:**
```
E_tokens = 2.90 × 1.0 = 2.90 E
H_tokens = 2.437 × 0.6 × 1.0 = 1.46 H
W_tokens = 0.09 × 0.4 = 0.036 W

GCM = E + H + W = 2.90 + 1.46 + 0.036 = 4.40 GCM
```

**Себестоимость:**
```
Cost_E = 2.90 × 5.69 ₽/кВт·ч = 16.50 ₽
Cost_H = 2.437 × 5.99 ₽/м³ = 14.60 ₽
Cost_W = 0.09 × 40.00 ₽/м³ = 3.60 ₽

Total = 34.70 ₽
```

## Интеграция с Edge Agent

### MQTT Topics:
```
SmartHome_Node01/telemetry/electricity
SmartHome_Node01/telemetry/gas
SmartHome_Node01/telemetry/water
SmartHome_Node01/telemetry/temperature
SmartHome_Node01/telemetry/system
```

### Payload Example:
```json
{
  "nodeId": "SmartHome_Node01",
  "timestamp": "2025-10-16T20:28:00Z",
  "slotId": "S5",
  "resources": {
    "electricity": {
      "consumption": 2.90,
      "unit": "kWh"
    },
    "gas": {
      "consumption": 2.437,
      "unit": "m3"
    },
    "water": {
      "consumption": 0.09,
      "unit": "m3"
    }
  },
  "temperature": {
    "cpu": 43.0,
    "ambient": 22.0
  }
}
```

## Конфигурация

### package.json
```json
{
  "name": "@mornet/sector-plugin-residential",
  "version": "0.1.0",
  "description": "Residential sector plugin for MorNet GreenCore",
  "main": "dist/index.js",
  "types": "dist/index.d.ts",
  "scripts": {
    "build": "tsc",
    "dev": "tsc --watch",
    "test": "jest"
  },
  "dependencies": {
    "@mornet/shared": "workspace:*"
  },
  "devDependencies": {
    "typescript": "^5.0.0",
    "jest": "^29.0.0"
  }
}
```

### TypeScript Types
```typescript
export interface ResidentialTelemetry {
  nodeId: string;
  timestamp: Date;
  slotId: string;
  electricity: {
    consumption: number; // kWh
    unit: 'kWh';
  };
  gas: {
    consumption: number; // m³
    unit: 'm3';
  };
  water: {
    consumption: number; // m³
    unit: 'm3';
  };
  temperature?: {
    cpu: number;
    ambient: number;
  };
}

export interface ResidentialTokens {
  E: number;
  H: number;
  W: number;
  GCM: number;
}

export interface ResidentialCost {
  E: number; // RUB
  H: number; // RUB
  W: number; // RUB
  total: number; // RUB
}
```

## Статус разработки

- **Версия:** 0.1.0 (MVP)
- **Статус:** 🟡 В разработке
- **Готовность:** 0%

## Roadmap

### MVP 1.0 (Q1 2026)
- [x] Архитектурный дизайн
- [ ] Базовая реализация (E/H/W)
- [ ] Формулы токенизации
- [ ] Интеграция с Cost Engine
- [ ] Unit тесты

### v1.1 (Q2 2026)
- [ ] Поддержка дополнительных устройств
- [ ] Оптимизация формул
- [ ] ML-модели для прогнозирования

### v1.2 (Q3 2026)
- [ ] Интеграция с умными счётчиками
- [ ] Автоматическое обнаружение устройств
- [ ] Рекомендации по оптимизации

---

**Документация:** [Pre-Development Architecture](../../../docs/architecture/pre-development-architecture.md)  
**Команда:** Backend Team  
**Контакт:** backend@mornet.io

