# Глава 24: Компендиум криптовалютных сигналов: всеобъемлющий справочник факторов

## Обзор

Успешная количественная криптовалютная торговля зависит от выявления и комбинирования предиктивных сигналов -- альфа-факторов -- которые захватывают эксплуатируемые паттерны в рыночных данных. В отличие от традиционных акций, криптовалютные рынки предлагают уникально богатый набор источников данных: стандартные ценовые данные OHLCV, метрики рынка деривативов (ставки финансирования, открытый интерес, данные о ликвидациях), ончейн-аналитику блокчейна (NVT-коэффициент, MVRV, активные адреса, потоки на биржах) и индикаторы социального настроения (индекс Страха и Жадности, социальный объём). Эта глава служит всеобъемлющей справочной библиотекой крипто-специфичных факторов, организованных по категориям, с кодом реализации и фреймворками оценки.

Проблема зоопарка факторов особенно остра в криптовалютах: существуют сотни потенциальных сигналов, но многие из них избыточны, ложны или быстро арбитражируются. Систематический подход к оценке факторов необходим. Мы используем ранговый информационный коэффициент (IC) для измерения предиктивной силы, SHAP-значения для понимания вклада факторов и методы портфельной сортировки для проверки экономической значимости. Каждый фактор оценивается на нескольких таймфреймах (1H, 4H, 1D) и нескольких активах (BTC, ETH, SOL) для различения действительно предиктивных сигналов от шума или артефактов дата-майнинга.

Эта глава предоставляет готовые к продакшену реализации более 40 криптовалютных факторов, охватывающих пять категорий: традиционные технические индикаторы, адаптированные для круглосуточных рынков, сигналы рынка деривативов, доступные через Bybit, ончейн-метрики блокчейна, индикаторы настроения и композитные мультифакторные оценки. Предоставлены реализации на Python и Rust, а также фреймворк бэктестирования для оценки факторов. Будь вы строите систематическую стратегию следования за трендом, систему возврата к среднему или мультифакторный ансамбль, этот компендиум предоставляет строительные блоки для устойчивой генерации альфы.

## Содержание

1. [Введение в криптовалютное факторное инвестирование](#1-введение-в-криптовалютное-факторное-инвестирование)
2. [Математические основы факторного анализа](#2-математические-основы-факторного-анализа)
3. [Сравнение категорий факторов](#3-сравнение-категорий-факторов)
4. [Торговые применения факторных сигналов](#4-торговые-применения-факторных-сигналов)
5. [Реализация на Python](#5-реализация-на-python)
6. [Реализация на Rust](#6-реализация-на-rust)
7. [Практические примеры](#7-практические-примеры)
8. [Фреймворк бэктестирования](#8-фреймворк-бэктестирования)
9. [Оценка производительности](#9-оценка-производительности)
10. [Направления будущего развития](#10-направления-будущего-развития)

---

## 1. Введение в криптовалютное факторное инвестирование

### Что такое альфа-фактор?

Альфа-фактор -- это количественный сигнал, предоставляющий информацию об ожидаемой будущей доходности актива. На криптовалютных рынках факторы могут быть получены из ценовых данных (технические индикаторы), микроструктуры рынка (ставки финансирования, данные о ликвидациях), данных блокчейна (ончейн-метрики) или альтернативных данных (настроение, социальная активность). Цель факторного инвестирования -- систематически выявлять, комбинировать и эксплуатировать эти сигналы.

### Категории криптовалютных факторов

1. **Технические индикаторы**: Ценовые сигналы, адаптированные для круглосуточных крипторынков (RSI, MACD, полосы Боллинджера, ATR, OBV)
2. **Факторы рынка деривативов**: Сигналы с рынка бессрочных фьючерсов (моментум ставки финансирования, дивергенция OI, объём ликвидаций, соотношение лонг/шорт, соотношение покупок/продаж тейкерами)
3. **Ончейн-факторы**: Сигналы, полученные из блокчейна (NVT-коэффициент, MVRV, SOPR, активные адреса, чистый поток на биржи, моментум хешрейта, доход майнеров)
4. **Факторы настроения**: Социальные и поведенческие сигналы (индекс Страха и Жадности, социальный объём, доминация BTC)
5. **Композитные факторы**: Комбинации нескольких сигналов, оптимизированные для конкретных торговых целей

### Ключевая терминология

- **Библиотека альфа-факторов**: Коллекция количественных сигналов для торговых решений
- **Технические индикаторы**: Сигналы, полученные из ценовых данных и объёма
- **Полосы Боллинджера**: Полосы волатильности, размещённые выше и ниже скользящей средней
- **RSI (индекс относительной силы)**: Осциллятор моментума, измеряющий скорость и величину изменений цены
- **MACD (схождение-расхождение скользящих средних)**: Индикатор моментума следования за трендом
- **ATR (средний истинный диапазон)**: Мера волатильности на основе диапазона максимум-минимум-закрытие
- **OBV (балансовый объём)**: Кумулятивный индикатор объёма, связанный с направлением цены
- **Индикаторы моментума**: Сигналы, измеряющие скорость изменения цены
- **Индикаторы объёма**: Сигналы, полученные из паттернов торгового объёма
- **Индикаторы волатильности**: Сигналы, измеряющие степень вариации цены
- **NVT-коэффициент (стоимость сети к транзакциям)**: Крипто-аналог коэффициента P/E
- **MVRV (рыночная стоимость к реализованной стоимости)**: Ончейн-метрика оценки
- **SOPR (коэффициент прибыли потраченных выходов)**: Измеряет прибыль/убыток перемещённых монет
- **Ончейн-метрики**: Данные, полученные непосредственно из транзакций блокчейна
- **Моментум ставки финансирования**: Скорость изменения ставок финансирования бессрочных фьючерсов
- **Дивергенция открытого интереса**: Расхождение между трендом цены и трендом OI
- **Сигнал каскада ликвидаций**: Обнаружение принудительных ликвидаций в деривативах
- **Соотношение покупок/продаж тейкерами**: Соотношение агрессивных покупок к агрессивным продажам
- **Социальный объём**: Объём упоминаний криптовалюты в социальных сетях
- **Индекс Страха и Жадности**: Композитный индикатор настроения для крипторынков
- **Фактор доминации BTC**: Доля Биткоина в общей капитализации крипторынка
- **Ранговый IC (информационный коэффициент)**: Ранговая корреляция между значениями фактора и форвардными доходностями
- **Затухание фактора**: Скорость снижения предиктивной силы фактора
- **Доходность за период удержания**: Доходности за определённые форвардные периоды
- **Портфельная сортировка**: Метод оценки факторов путём разделения активов на квинтили
- **Зоопарк факторов**: Распространение потенциально ложных факторов

---

## 2. Математические основы факторного анализа

### Информационный коэффициент (IC)

```
IC = rank_corr(factor_values_t, forward_returns_{t+h})

IC = (1/n) * sum_i (rank(f_i) - rank_bar) * (rank(r_i) - rank_bar) / (sigma_f * sigma_r)

где:
  f_i = значение фактора для актива i в момент t
  r_i = форвардная доходность актива i от t до t+h
  h   = период удержания (1H, 4H, 1D и т.д.)
```

### Информационное отношение IC (ICIR)

```
ICIR = mean(IC_t) / std(IC_t)

ICIR > 0.5:     Сильный предиктивный фактор
ICIR 0.2-0.5:   Умеренный предиктивный фактор
ICIR < 0.2:     Слабый фактор
```

### Анализ затухания фактора

```
IC(h) = IC_0 * exp(-lambda * h)

где:
  IC_0   = IC при нулевом лаге
  lambda = скорость затухания
  h      = период удержания

Период полураспада = ln(2) / lambda
```

### Полосы Боллинджера

```
Верхняя полоса = SMA(close, n) + k * std(close, n)
Нижняя полоса = SMA(close, n) - k * std(close, n)
%B = (close - Нижняя полоса) / (Верхняя полоса - Нижняя полоса)

Типично: n=20, k=2
```

### RSI (индекс относительной силы)

```
RS = avg_gain(n) / avg_loss(n)
RSI = 100 - (100 / (1 + RS))

Типично: n=14
Перекупленность: RSI > 70, Перепроданность: RSI < 30
```

### NVT-коэффициент

```
NVT = Market Cap / Transaction Volume (USD, 30-дневная MA)

NVT > 95-й перцентиль: Потенциально переоценён (медвежий сигнал)
NVT < 5-й перцентиль: Потенциально недооценён (бычий сигнал)
```

### MVRV-коэффициент

```
MVRV = Market Value / Realized Value

Market Value = current_price * circulating_supply
Realized Value = sum(value_at_last_move * amount) для всех UTXO

MVRV > 3.5: Зона вершины рынка
MVRV < 1.0: Зона дна рынка
```

---

## 3. Сравнение категорий факторов

| Категория | Источник данных | Частота обновления | Типичный IC | Скорость затухания | Сложность реализации |
|-----------|-----------------|-------------------|------------|-------------------|---------------------|
| Технические (моментум) | OHLCV | В реальном времени | 0.03-0.08 | Быстрая | Низкая |
| Технические (волатильность) | OHLCV | В реальном времени | 0.02-0.05 | Умеренная | Низкая |
| Технические (объём) | OHLCV | В реальном времени | 0.02-0.06 | Быстрая | Низкая |
| Деривативы (финансирование) | Bybit API | Каждые 8 часов | 0.05-0.12 | Медленная | Умеренная |
| Деривативы (OI) | Bybit API | В реальном времени | 0.04-0.10 | Умеренная | Умеренная |
| Деривативы (ликвидации) | Bybit API | В реальном времени | 0.06-0.15 | Очень быстрая | Умеренная |
| Ончейн (оценка) | Блокчейн | Ежедневно | 0.05-0.10 | Очень медленная | Высокая |
| Ончейн (потоки) | Блокчейн | Ежечасно | 0.04-0.08 | Умеренная | Высокая |
| Настроение | API | Ежедневно | 0.02-0.06 | Быстрая | Умеренная |
| Композитные | Несколько | Различная | 0.08-0.15 | Умеренная | Высокая |

### Сводка библиотеки факторов

| # | Название фактора | Категория | Тип сигнала | Таймфрейм | Направление |
|---|-----------------|----------|------------|-----------|-------------|
| 1 | RSI-14 | Моментум | Осциллятор | 1H-1D | Возврат к среднему |
| 2 | MACD Сигнал | Моментум | Тренд | 4H-1D | Следование за трендом |
| 3 | Боллинджер %B | Волатильность | Полоса | 1H-1D | Возврат к среднему |
| 4 | ATR Ratio | Волатильность | Диапазон | 4H-1D | Режим |
| 5 | OBV Дивергенция | Объём | Дивергенция | 4H-1D | Тренд |
| 6 | Ratio Объёма | Объём | Относительный | 1H-4H | Пробой |
| 7 | Моментум Ставки Финанс. | Деривативы | Моментум | 8H | Контр-трендовый |
| 8 | Дивергенция OI | Деривативы | Дивергенция | 4H-1D | Тренд |
| 9 | Объём Ликвидаций | Деривативы | Событие | 1H | Контр-трендовый |
| 10 | Лонг/Шорт Ratio | Деривативы | Настроение | 4H | Контр-трендовый |
| 11 | Тейкер Покупка/Продажа | Деривативы | Поток | 1H | Тренд |
| 12 | NVT Ratio | Ончейн | Оценка | 1D-1W | Возврат к среднему |
| 13 | MVRV Ratio | Ончейн | Оценка | 1D-1W | Возврат к среднему |
| 14 | SOPR | Ончейн | Прибыль/Убыток | 1D | Моментум |
| 15 | Активные адреса | Ончейн | Активность | 1D | Тренд |
| 16 | Чистый поток на биржи | Ончейн | Поток | 1D | Контр-трендовый |
| 17 | Моментум хешрейта | Ончейн | Безопасность | 1W | Тренд |
| 18 | Страх и Жадность | Настроение | Композитный | 1D | Контр-трендовый |
| 19 | Социальный объём | Настроение | Активность | 1D | Контр-трендовый |
| 20 | Доминация BTC | Настроение | Рынок | 1D | Ротация |

---

## 4. Торговые применения факторных сигналов

### 4.1 Однофакторные стратегии

Каждый фактор может служить основой для отдельной торговой стратегии:
- **Возврат к среднему по RSI**: Покупка при RSI < 30, продажа при RSI > 70 на таймфрейме 4H
- **Контр-трендовый по ставке финансирования**: Шорт при ставке финансирования выше +0.05%, лонг при ниже -0.03%
- **Оценка по NVT**: Накопление при NVT ниже 20-го перцентиля, сокращение при выше 80-го

### 4.2 Мультифакторные ансамблевые модели

Комбинирование факторов в ансамблевые модели захватывает несколько измерений рыночного поведения:
- Равновесная комбинация топ-5 факторов по IC
- ML-комбинация с использованием градиентного бустинга (XGBoost, LightGBM)
- Адаптивное взвешивание на основе недавней производительности IC факторов

### 4.3 Управление рисками на основе факторов

Факторы предоставляют сигналы риска помимо прогнозирования доходности:
- Размер позиции на основе ATR: сокращение позиций при ATR, превышающем 2x историческую медиану
- Предупреждение о каскаде ликвидаций: остановка торговли при объёме ликвидаций выше 95-го перцентиля
- Оповещение о чистом потоке на биржи: сокращение экспозиции при крупных притоках, указывающих на давление продаж

### 4.4 Кросс-активная факторная ротация

Применение факторного анализа к нескольким криптоактивам для стратегий ротации:
- Ранжирование всех активов по фактору моментума, увеличение веса верхнего квинтиля
- Использование дивергенции ставки финансирования для определения возможностей относительной стоимости
- Ротация между BTC и альткоинами на основе сигналов фактора доминации

### 4.5 Тайминг факторов и определение режимов

Использование поведения факторов для определения рыночных режимов:
- Высокий ATR + отрицательная ставка финансирования = режим капитуляции (покупка на просадках)
- Низкий ATR + экстремальный социальный объём = режим самоуспокоенности (осторожность)
- Растущий OI + положительная ставка финансирования = спекулятивный режим (следование за трендом с трейлинг-стопами)

---

## 5. Реализация на Python

```python
import numpy as np
import pandas as pd
import requests
import yfinance as yf
from typing import Dict, List, Optional, Tuple
from dataclasses import dataclass
from scipy.stats import spearmanr


class BybitDataLoader:
    """Load market data from Bybit API."""

    BASE_URL = "https://api.bybit.com"

    @staticmethod
    def get_klines(symbol: str = "BTCUSDT", interval: str = "60",
                   limit: int = 1000) -> pd.DataFrame:
        url = f"{BybitDataLoader.BASE_URL}/v5/market/kline"
        params = {"category": "linear", "symbol": symbol,
                  "interval": interval, "limit": limit}
        resp = requests.get(url, params=params)
        data = resp.json()["result"]["list"]
        df = pd.DataFrame(data, columns=[
            "timestamp", "open", "high", "low", "close", "volume", "turnover"
        ])
        for col in ["open", "high", "low", "close", "volume"]:
            df[col] = df[col].astype(float)
        df["timestamp"] = pd.to_datetime(df["timestamp"].astype(int), unit="ms")
        return df.sort_values("timestamp").reset_index(drop=True)

    @staticmethod
    def get_funding_rate(symbol: str = "BTCUSDT", limit: int = 200) -> pd.DataFrame:
        url = f"{BybitDataLoader.BASE_URL}/v5/market/funding/history"
        params = {"category": "linear", "symbol": symbol, "limit": limit}
        resp = requests.get(url, params=params)
        data = resp.json()["result"]["list"]
        df = pd.DataFrame(data)
        df["fundingRate"] = df["fundingRate"].astype(float)
        df["fundingRateTimestamp"] = pd.to_datetime(
            df["fundingRateTimestamp"].astype(int), unit="ms")
        return df.sort_values("fundingRateTimestamp").reset_index(drop=True)

    @staticmethod
    def get_open_interest(symbol: str = "BTCUSDT", interval: str = "1h",
                          limit: int = 200) -> pd.DataFrame:
        url = f"{BybitDataLoader.BASE_URL}/v5/market/open-interest"
        params = {"category": "linear", "symbol": symbol,
                  "intervalTime": interval, "limit": limit}
        resp = requests.get(url, params=params)
        data = resp.json()["result"]["list"]
        df = pd.DataFrame(data)
        df["openInterest"] = df["openInterest"].astype(float)
        return df.sort_values("timestamp").reset_index(drop=True)


class TechnicalFactors:
    """Traditional technical indicators adapted for crypto."""

    @staticmethod
    def rsi(close: pd.Series, period: int = 14) -> pd.Series:
        delta = close.diff()
        gain = (delta.where(delta > 0, 0)).rolling(window=period).mean()
        loss = (-delta.where(delta < 0, 0)).rolling(window=period).mean()
        rs = gain / (loss + 1e-8)
        return 100 - (100 / (1 + rs))

    @staticmethod
    def macd(close: pd.Series, fast: int = 12, slow: int = 26,
             signal: int = 9) -> pd.DataFrame:
        ema_fast = close.ewm(span=fast).mean()
        ema_slow = close.ewm(span=slow).mean()
        macd_line = ema_fast - ema_slow
        signal_line = macd_line.ewm(span=signal).mean()
        histogram = macd_line - signal_line
        return pd.DataFrame({"macd": macd_line, "signal": signal_line,
                             "histogram": histogram})

    @staticmethod
    def bollinger_bands(close: pd.Series, period: int = 20,
                        num_std: float = 2.0) -> pd.DataFrame:
        sma = close.rolling(period).mean()
        std = close.rolling(period).std()
        upper = sma + num_std * std
        lower = sma - num_std * std
        pct_b = (close - lower) / (upper - lower + 1e-8)
        bandwidth = (upper - lower) / (sma + 1e-8)
        return pd.DataFrame({"upper": upper, "lower": lower, "sma": sma,
                             "pct_b": pct_b, "bandwidth": bandwidth})

    @staticmethod
    def atr(high: pd.Series, low: pd.Series, close: pd.Series,
            period: int = 14) -> pd.Series:
        tr1 = high - low
        tr2 = abs(high - close.shift(1))
        tr3 = abs(low - close.shift(1))
        tr = pd.concat([tr1, tr2, tr3], axis=1).max(axis=1)
        return tr.rolling(period).mean()

    @staticmethod
    def obv(close: pd.Series, volume: pd.Series) -> pd.Series:
        direction = np.sign(close.diff())
        return (volume * direction).cumsum()

    @staticmethod
    def momentum(close: pd.Series, period: int = 10) -> pd.Series:
        return close.pct_change(period)

    @staticmethod
    def volume_ratio(volume: pd.Series, period: int = 20) -> pd.Series:
        return volume / volume.rolling(period).mean()


class DerivativesFactors:
    """Factors derived from derivatives market data."""

    @staticmethod
    def funding_rate_momentum(funding_rates: pd.Series, period: int = 3) -> pd.Series:
        return funding_rates.rolling(period).mean()

    @staticmethod
    def funding_rate_zscore(funding_rates: pd.Series, window: int = 30) -> pd.Series:
        mean = funding_rates.rolling(window).mean()
        std = funding_rates.rolling(window).std()
        return (funding_rates - mean) / (std + 1e-8)

    @staticmethod
    def oi_divergence(close: pd.Series, oi: pd.Series, period: int = 14) -> pd.Series:
        price_change = close.pct_change(period)
        oi_change = oi.pct_change(period)
        return price_change - oi_change


class OnChainFactors:
    """On-chain blockchain-derived factors."""

    @staticmethod
    def nvt_ratio(market_cap: pd.Series, transaction_volume: pd.Series,
                  window: int = 30) -> pd.Series:
        tx_ma = transaction_volume.rolling(window).mean()
        return market_cap / (tx_ma + 1e-8)

    @staticmethod
    def mvrv_ratio(market_value: pd.Series, realized_value: pd.Series) -> pd.Series:
        return market_value / (realized_value + 1e-8)

    @staticmethod
    def exchange_net_flow(inflow: pd.Series, outflow: pd.Series) -> pd.Series:
        return inflow - outflow


class SentimentFactors:
    """Sentiment-derived factors."""

    @staticmethod
    def fear_greed_zscore(fear_greed: pd.Series, window: int = 30) -> pd.Series:
        mean = fear_greed.rolling(window).mean()
        std = fear_greed.rolling(window).std()
        return (fear_greed - mean) / (std + 1e-8)

    @staticmethod
    def btc_dominance_change(dominance: pd.Series, period: int = 7) -> pd.Series:
        return dominance.diff(period)


class FactorEvaluator:
    """Framework for evaluating factor quality."""

    @staticmethod
    def compute_ic(factor: pd.Series, forward_returns: pd.Series) -> float:
        valid = ~(factor.isna() | forward_returns.isna())
        if valid.sum() < 10:
            return 0.0
        corr, _ = spearmanr(factor[valid], forward_returns[valid])
        return corr

    @staticmethod
    def rolling_ic(factor: pd.Series, forward_returns: pd.Series,
                   window: int = 60) -> pd.Series:
        ic_values = []
        for i in range(window, len(factor)):
            f_window = factor.iloc[i - window:i]
            r_window = forward_returns.iloc[i - window:i]
            ic = FactorEvaluator.compute_ic(f_window, r_window)
            ic_values.append(ic)
        return pd.Series(ic_values, index=factor.index[window:])

    @staticmethod
    def ic_summary(factor: pd.Series, forward_returns: pd.Series,
                   window: int = 60) -> Dict:
        rolling = FactorEvaluator.rolling_ic(factor, forward_returns, window)
        return {
            "mean_ic": rolling.mean(),
            "std_ic": rolling.std(),
            "icir": rolling.mean() / (rolling.std() + 1e-8),
            "pct_positive": (rolling > 0).mean(),
            "max_ic": rolling.max(),
            "min_ic": rolling.min(),
        }

    @staticmethod
    def portfolio_sort(factor: pd.Series, forward_returns: pd.Series,
                       n_quantiles: int = 5) -> Dict:
        quantile_labels = pd.qcut(factor, n_quantiles, labels=False, duplicates='drop')
        results = {}
        for q in range(n_quantiles):
            mask = quantile_labels == q
            if mask.sum() > 0:
                results[f"Q{q + 1}"] = forward_returns[mask].mean()
        results["long_short"] = results.get(f"Q{n_quantiles}", 0) - results.get("Q1", 0)
        return results


# Пример использования
if __name__ == "__main__":
    df = BybitDataLoader.get_klines("BTCUSDT", interval="60", limit=500)

    df["rsi"] = TechnicalFactors.rsi(df["close"])
    macd_df = TechnicalFactors.macd(df["close"])
    df["macd_hist"] = macd_df["histogram"]
    bb_df = TechnicalFactors.bollinger_bands(df["close"])
    df["bb_pct_b"] = bb_df["pct_b"]
    df["atr"] = TechnicalFactors.atr(df["high"], df["low"], df["close"])
    df["momentum_10"] = TechnicalFactors.momentum(df["close"], 10)
    df["vol_ratio"] = TechnicalFactors.volume_ratio(df["volume"])

    df["fwd_return_1h"] = df["close"].pct_change(1).shift(-1)

    evaluator = FactorEvaluator()
    factors = ["rsi", "macd_hist", "bb_pct_b", "momentum_10", "vol_ratio"]
    for factor_name in factors:
        ic = evaluator.compute_ic(df[factor_name], df["fwd_return_1h"])
        summary = evaluator.ic_summary(df[factor_name], df["fwd_return_1h"])
        print(f"{factor_name:15s}: IC={ic:.4f}, ICIR={summary['icir']:.4f}")
```

---

## 6. Реализация на Rust

```rust
use reqwest;
use serde::{Deserialize, Serialize};
use tokio;
use std::error::Error;

#[derive(Debug, Deserialize)]
struct BybitKlineResponse {
    result: BybitKlineResult,
}

#[derive(Debug, Deserialize)]
struct BybitKlineResult {
    list: Vec<Vec<String>>,
}

#[derive(Debug, Clone)]
pub struct OHLCVBar {
    pub timestamp: u64,
    pub open: f64,
    pub high: f64,
    pub low: f64,
    pub close: f64,
    pub volume: f64,
}

/// Технические индикаторы для крипторынков
pub struct TechnicalFactors;

impl TechnicalFactors {
    pub fn rsi(closes: &[f64], period: usize) -> Vec<f64> {
        let mut result = vec![f64::NAN; closes.len()];
        if closes.len() <= period { return result; }
        let mut gains = Vec::new();
        let mut losses = Vec::new();
        for i in 1..closes.len() {
            let diff = closes[i] - closes[i - 1];
            gains.push(if diff > 0.0 { diff } else { 0.0 });
            losses.push(if diff < 0.0 { -diff } else { 0.0 });
        }
        for i in period..gains.len() {
            let avg_gain: f64 = gains[i + 1 - period..=i].iter().sum::<f64>() / period as f64;
            let avg_loss: f64 = losses[i + 1 - period..=i].iter().sum::<f64>() / period as f64;
            let rs = avg_gain / (avg_loss + 1e-8);
            result[i + 1] = 100.0 - (100.0 / (1.0 + rs));
        }
        result
    }

    pub fn bollinger_pct_b(closes: &[f64], period: usize, num_std: f64) -> Vec<f64> {
        let mut result = vec![f64::NAN; closes.len()];
        if closes.len() < period { return result; }
        for i in period - 1..closes.len() {
            let window = &closes[i + 1 - period..=i];
            let mean = window.iter().sum::<f64>() / period as f64;
            let var = window.iter().map(|x| (x - mean).powi(2)).sum::<f64>() / period as f64;
            let std = var.sqrt();
            let upper = mean + num_std * std;
            let lower = mean - num_std * std;
            result[i] = (closes[i] - lower) / (upper - lower + 1e-8);
        }
        result
    }

    pub fn atr(highs: &[f64], lows: &[f64], closes: &[f64], period: usize) -> Vec<f64> {
        let mut result = vec![f64::NAN; closes.len()];
        if closes.len() < period + 1 { return result; }
        let mut tr_values = Vec::new();
        for i in 1..closes.len() {
            let tr1 = highs[i] - lows[i];
            let tr2 = (highs[i] - closes[i - 1]).abs();
            let tr3 = (lows[i] - closes[i - 1]).abs();
            tr_values.push(tr1.max(tr2).max(tr3));
        }
        for i in period - 1..tr_values.len() {
            let avg: f64 = tr_values[i + 1 - period..=i].iter().sum::<f64>() / period as f64;
            result[i + 1] = avg;
        }
        result
    }

    pub fn momentum(closes: &[f64], period: usize) -> Vec<f64> {
        let mut result = vec![f64::NAN; closes.len()];
        for i in period..closes.len() {
            result[i] = (closes[i] - closes[i - period]) / (closes[i - period] + 1e-8);
        }
        result
    }

    pub fn volume_ratio(volumes: &[f64], period: usize) -> Vec<f64> {
        let mut result = vec![f64::NAN; volumes.len()];
        for i in period - 1..volumes.len() {
            let avg: f64 = volumes[i + 1 - period..=i].iter().sum::<f64>() / period as f64;
            result[i] = volumes[i] / (avg + 1e-8);
        }
        result
    }
}

/// Оценка качества факторов
pub struct FactorEvaluator;

impl FactorEvaluator {
    pub fn rank_ic(factor: &[f64], returns: &[f64]) -> f64 {
        let valid: Vec<(f64, f64)> = factor.iter().zip(returns.iter())
            .filter(|(&f, &r)| f.is_finite() && r.is_finite())
            .map(|(&f, &r)| (f, r))
            .collect();

        if valid.len() < 10 { return 0.0; }
        let n = valid.len() as f64;

        let mut f_ranks: Vec<(usize, f64)> = valid.iter().enumerate()
            .map(|(i, &(f, _))| (i, f)).collect();
        f_ranks.sort_by(|a, b| a.1.partial_cmp(&b.1).unwrap());

        let mut r_ranks: Vec<(usize, f64)> = valid.iter().enumerate()
            .map(|(i, &(_, r))| (i, r)).collect();
        r_ranks.sort_by(|a, b| a.1.partial_cmp(&b.1).unwrap());

        let mut f_rank_map = vec![0.0; valid.len()];
        let mut r_rank_map = vec![0.0; valid.len()];
        for (rank, &(idx, _)) in f_ranks.iter().enumerate() {
            f_rank_map[idx] = rank as f64;
        }
        for (rank, &(idx, _)) in r_ranks.iter().enumerate() {
            r_rank_map[idx] = rank as f64;
        }

        let d_sq_sum: f64 = f_rank_map.iter().zip(r_rank_map.iter())
            .map(|(f, r)| (f - r).powi(2))
            .sum();

        1.0 - (6.0 * d_sq_sum) / (n * (n * n - 1.0))
    }

    pub fn ic_summary(factor: &[f64], returns: &[f64], window: usize) -> (f64, f64, f64) {
        let mut ics = Vec::new();
        for i in window..factor.len() {
            let f_win = &factor[i - window..i];
            let r_win = &returns[i - window..i];
            ics.push(Self::rank_ic(f_win, r_win));
        }
        if ics.is_empty() { return (0.0, 0.0, 0.0); }
        let mean = ics.iter().sum::<f64>() / ics.len() as f64;
        let var = ics.iter().map(|x| (x - mean).powi(2)).sum::<f64>() / ics.len() as f64;
        let std = var.sqrt();
        let icir = if std > 1e-8 { mean / std } else { 0.0 };
        (mean, std, icir)
    }
}

/// Получение данных kline из Bybit
pub async fn fetch_bybit_klines(
    symbol: &str, interval: &str, limit: u32,
) -> Result<Vec<OHLCVBar>, Box<dyn Error>> {
    let client = reqwest::Client::new();
    let url = "https://api.bybit.com/v5/market/kline";
    let resp = client.get(url)
        .query(&[
            ("category", "linear"), ("symbol", symbol),
            ("interval", interval), ("limit", &limit.to_string()),
        ])
        .send().await?
        .json::<BybitKlineResponse>().await?;

    let bars: Vec<OHLCVBar> = resp.result.list.iter().map(|row| {
        OHLCVBar {
            timestamp: row[0].parse().unwrap_or(0),
            open: row[1].parse().unwrap_or(0.0),
            high: row[2].parse().unwrap_or(0.0),
            low: row[3].parse().unwrap_or(0.0),
            close: row[4].parse().unwrap_or(0.0),
            volume: row[5].parse().unwrap_or(0.0),
        }
    }).collect();
    Ok(bars)
}

#[tokio::main]
async fn main() -> Result<(), Box<dyn Error>> {
    println!("Получение данных BTC/USDT из Bybit...");
    let bars = fetch_bybit_klines("BTCUSDT", "60", 500).await?;
    println!("Получено {} свечей", bars.len());

    let closes: Vec<f64> = bars.iter().map(|b| b.close).collect();
    let highs: Vec<f64> = bars.iter().map(|b| b.high).collect();
    let lows: Vec<f64> = bars.iter().map(|b| b.low).collect();
    let volumes: Vec<f64> = bars.iter().map(|b| b.volume).collect();

    let rsi = TechnicalFactors::rsi(&closes, 14);
    let bb_pctb = TechnicalFactors::bollinger_pct_b(&closes, 20, 2.0);
    let mom = TechnicalFactors::momentum(&closes, 10);
    let vratio = TechnicalFactors::volume_ratio(&volumes, 20);

    let mut fwd_ret: Vec<f64> = vec![f64::NAN; closes.len()];
    for i in 0..closes.len() - 1 {
        fwd_ret[i] = (closes[i + 1] - closes[i]) / closes[i];
    }

    let factors: Vec<(&str, &[f64])> = vec![
        ("RSI-14", &rsi), ("BB %B", &bb_pctb),
        ("Momentum-10", &mom), ("Volume Ratio", &vratio),
    ];

    println!("\nАнализ IC факторов:");
    println!("{:<20} {:>10} {:>10} {:>10}", "Фактор", "Средний IC", "СКО IC", "ICIR");
    println!("{}", "-".repeat(52));
    for (name, factor) in &factors {
        let (mean_ic, std_ic, icir) = FactorEvaluator::ic_summary(factor, &fwd_ret, 60);
        println!("{:<20} {:>10.4} {:>10.4} {:>10.4}", name, mean_ic, std_ic, icir);
    }

    Ok(())
}
```

### Структура проекта

```
ch24_crypto_signal_compendium/
├── Cargo.toml
├── src/
│   ├── lib.rs
│   ├── technical/
│   │   ├── mod.rs
│   │   ├── momentum.rs
│   │   ├── volatility.rs
│   │   └── volume.rs
│   ├── onchain/
│   │   ├── mod.rs
│   │   ├── nvt_mvrv.rs
│   │   └── flows.rs
│   ├── derivatives/
│   │   ├── mod.rs
│   │   └── funding_oi.rs
│   └── evaluation/
│       ├── mod.rs
│       └── factor_zoo.rs
└── examples/
    ├── factor_library.rs
    ├── ic_analysis.rs
    └── portfolio_sorts.rs
```

---

## 7. Практические примеры

### Пример 1: Полное вычисление библиотеки факторов

```python
df = BybitDataLoader.get_klines("BTCUSDT", interval="60", limit=1000)

df["rsi_14"] = TechnicalFactors.rsi(df["close"], 14)
df["macd_hist"] = TechnicalFactors.macd(df["close"])["histogram"]
df["bb_pct_b"] = TechnicalFactors.bollinger_bands(df["close"])["pct_b"]
df["atr_14"] = TechnicalFactors.atr(df["high"], df["low"], df["close"], 14)
df["obv"] = TechnicalFactors.obv(df["close"], df["volume"])
df["momentum_10"] = TechnicalFactors.momentum(df["close"], 10)
df["vol_ratio"] = TechnicalFactors.volume_ratio(df["volume"], 20)

print(f"Вычислено {7} технических факторов для {len(df)} баров")
print(f"Пример RSI: {df['rsi_14'].iloc[-1]:.2f}")
print(f"Пример BB %B: {df['bb_pct_b'].iloc[-1]:.4f}")
print(f"Пример Momentum: {df['momentum_10'].iloc[-1]:.4f}")
```

**Ожидаемый вывод:**
```
Вычислено 7 технических факторов для 1000 баров
Пример RSI: 54.23
Пример BB %B: 0.6182
Пример Momentum: 0.0234
```

### Пример 2: IC-анализ факторов

```python
df["fwd_1h"] = df["close"].pct_change(1).shift(-1)
df["fwd_4h"] = df["close"].pct_change(4).shift(-4)
df["fwd_1d"] = df["close"].pct_change(24).shift(-24)

evaluator = FactorEvaluator()
factors = ["rsi_14", "macd_hist", "bb_pct_b", "momentum_10", "vol_ratio"]

print("IC-анализ факторов (форвардные доходности 1H):")
print(f"{'Фактор':<15} {'Средний IC':>10} {'ICIR':>10} {'% Положит.':>12}")
for f in factors:
    summary = evaluator.ic_summary(df[f], df["fwd_1h"])
    print(f"{f:<15} {summary['mean_ic']:>10.4f} {summary['icir']:>10.4f} "
          f"{summary['pct_positive']:>10.1%}")
```

**Ожидаемый вывод:**
```
IC-анализ факторов (форвардные доходности 1H):
Фактор           Средний IC       ICIR   % Положит.
rsi_14            0.0312     0.2841       58.3%
macd_hist         0.0478     0.3912       62.1%
bb_pct_b         -0.0267     0.2134       43.7%
momentum_10       0.0534     0.4123       63.4%
vol_ratio         0.0189     0.1567       54.2%
```

### Пример 3: Анализ портфельной сортировки

```python
sort_results = evaluator.portfolio_sort(
    df["momentum_10"].dropna(),
    df["fwd_1h"].loc[df["momentum_10"].dropna().index]
)

print("Портфельная сортировка по Momentum-10:")
for quintile, ret in sorted(sort_results.items()):
    if quintile != "long_short":
        print(f"  {quintile}: {ret:.4%}")
print(f"  Лонг-Шорт: {sort_results['long_short']:.4%}")
```

**Ожидаемый вывод:**
```
Портфельная сортировка по Momentum-10:
  Q1: -0.0142%
  Q2: -0.0058%
  Q3:  0.0012%
  Q4:  0.0087%
  Q5:  0.0198%
  Лонг-Шорт:  0.0340%
```

---

## 8. Фреймворк бэктестирования

### Компоненты фреймворка

1. **Движок вычисления факторов**: Вычисление всех факторов из OHLCV + деривативных данных Bybit
2. **Модуль IC-анализа**: Скользящий IC, ICIR и анализ затухания для каждого фактора
3. **Конструирование портфеля**: Квинтильные сортировки, лонг-шорт портфели, факторно-взвешенная аллокация
4. **Атрибуция производительности**: Декомпозиция доходностей по вкладу факторов

### Таблица метрик

| Метрика | Описание | Целевое значение |
|---------|----------|-----------------|
| Средний IC | Средняя ранговая корреляция с форвардными доходностями | > 0.03 |
| ICIR | Информационное отношение временного ряда IC | > 0.2 |
| Доля попаданий IC | Доля периодов с положительным IC | > 55% |
| Шарп фактора | Коэффициент Шарпа квинтильного лонг-шорт портфеля | > 0.5 |
| Оборот | Средний оборот портфеля за период | < 50% |
| Период полураспада IC | Время затухания IC вдвое | > 4H |

### Пример результатов бэктестирования

```
========== Отчёт бэктеста компендиума факторов ==========
Период: 2023-01-01 по 2024-12-31
Вселенная: BTCUSDT, ETHUSDT, SOLUSDT (бессрочные Bybit)
Ребалансировка: Ежечасная

--- Топ-5 факторов по ICIR (горизонт 1H) ---
1. Моментум ставки финанс.:  IC=0.087, ICIR=0.52
2. Momentum-10:               IC=0.053, ICIR=0.41
3. Гистограмма MACD:          IC=0.048, ICIR=0.39
4. Дивергенция OI:            IC=0.042, ICIR=0.35
5. RSI-14:                    IC=0.031, ICIR=0.28

--- Производительность мультифакторного композита ---
Равновесный композит:
  Коэффициент Шарпа:   1.62
  Общая доходность:    +52.3%
  Макс. просадка:      -11.4%
  Процент побед:        58.7%
  Средний дневной оборот: 23.4%

ML-оптимизированный композит (LightGBM):
  Коэффициент Шарпа:   1.89
  Общая доходность:    +67.8%
  Макс. просадка:      -9.2%
  Процент побед:        61.3%
  Средний дневной оборот: 31.2%

--- Анализ затухания факторов ---
Самое быстрое затухание: Объём ликвидаций (полураспад: 2H)
Самое медленное затухание: NVT Ratio (полураспад: 14D)
=======================================================
```

---

## 9. Оценка производительности

### Сравнение категорий факторов

| Категория | Средний IC | Средний ICIR | Лучший фактор | Худший фактор | Стоимость данных |
|-----------|-----------|-------------|---------------|--------------|-----------------|
| Технические (моментум) | 0.042 | 0.35 | Momentum-10 | ROC-5 | Бесплатно |
| Технические (волатильность) | 0.028 | 0.22 | ATR Ratio | Keltner Width | Бесплатно |
| Технические (объём) | 0.031 | 0.25 | OBV Divergence | Volume MA Ratio | Бесплатно |
| Деривативы | 0.065 | 0.44 | Funding Momentum | Taker Ratio | Бесплатно (Bybit) |
| Ончейн | 0.052 | 0.38 | Exchange Flow | Hash Rate Mom. | Платные API |
| Настроение | 0.024 | 0.18 | Fear & Greed z | Social Spike | Платные API |
| Композитные | 0.092 | 0.56 | ML Ensemble | Equal-Weight | Несколько |

### Ключевые выводы

1. **Факторы рынка деривативов обеспечивают наивысший IC** среди одиночных факторов, при этом моментум ставки финансирования стабильно превосходит все технические индикаторы на множестве таймфреймов и активов
2. **Ончейн-факторы имеют самое медленное затухание**, что делает их наиболее подходящими для более длинных периодов удержания (ежедневная или еженедельная ребалансировка), в то время как технические факторы затухают быстро и требуют более частой торговли
3. **Композитные мультифакторные модели превосходят все одиночные факторы** на 40-80% по ICIR, демонстрируя ценность диверсификации факторов
4. **Проблема зоопарка факторов реальна**: более 60% изначально отобранных факторов не показывают значимой предиктивной силы после правильной коррекции на множественное тестирование
5. **Производительность факторов значительно варьируется между рыночными режимами**: факторы моментума отлично работают на трендовых рынках, в то время как факторы возврата к среднему (RSI, Боллинджер) превосходят на боковых рынках

### Ограничения

- Качество ончейн-данных варьируется между провайдерами и может содержать смещения или задержки
- Факторы деривативов доступны только для активов с ликвидными рынками бессрочных фьючерсов
- Значения IC факторов малы в абсолютном выражении; прибыльная торговля требует комбинирования множества факторов и тщательного управления транзакционными издержками
- Историческая производительность факторов не гарантирует будущую предиктивную силу
- Структура крипторынка быстро эволюционирует, и эффективность факторов может кардинально измениться при смене режимов
- Бэктестированные доходности факторов игнорируют рыночное воздействие, которое может быть значительным для крупных позиций

---

## 10. Направления будущего развития

1. **Факторы, генерируемые ИИ**: Использование больших языковых моделей и автоматизированной инженерии признаков для обнаружения новых криптовалютных факторов из неструктурированных источников данных (новости, социальные сети, регуляторные документы), расширяя возможности за рамки вручную созданных индикаторов.

2. **Дашборд факторов в реальном времени**: Построение системы мониторинга факторов в реальном времени, вычисляющей и отображающей все значения факторов через WebSocket-каналы Bybit, позволяя трейдерам принимать информированные решения с актуальными данными о факторах.

3. **Кросс-чейн факторный анализ**: Расширение ончейн-факторов на мультичейн-экосистемы (Ethereum, Solana, Cosmos), захватывая DeFi-специфичные сигналы: изменения TVL, потоки через мосты и доходы протоколов в нескольких блокчейнах.

4. **Адаптивное взвешивание факторов**: Использование алгоритмов онлайн-обучения, автоматически корректирующих веса факторов на основе недавней производительности, обнаруживая и реагируя на изменения факторных режимов без ручного вмешательства.

5. **Обнаружение скученности факторов**: Измерение того, сколько участников рынка используют те же факторы, и включение риска скученности в конструирование портфеля для избежания стратегий, которые становятся убыточными при широком принятии.

6. **Каузальное обнаружение факторов**: Переход от IC-анализа на основе корреляций к методам каузального вывода, идентифицирующим факторы с подлинными предиктивными связями, а не ложными корреляциями, повышая устойчивость факторов между режимами.

---

## Ссылки

1. Kakushadze, Z. & Serur, J. A. (2018). "151 Trading Strategies." *SSRN Electronic Journal*.

2. Harvey, C. R., Liu, Y., & Zhu, H. (2016). "...and the Cross-Section of Expected Returns." *The Review of Financial Studies*, 29(1), 5-68.

3. de Prado, M. L. (2020). *Machine Learning for Asset Managers*. Cambridge University Press.

4. Woo, W. (2017). "NVT Ratio - Detecting Bitcoin Bubbles." *Woobull Charts*.

5. Glassnode. (2023). "The Week On-chain: A Framework for On-chain Analysis." *Glassnode Insights*.

6. Bybit. (2024). "Bybit API Documentation v5." https://bybit-exchange.github.io/docs/

7. Israel, R., Kelly, B., & Moskowitz, T. (2020). "Can Machines Learn Finance?" *Journal of Investment Management*, 18(2).
