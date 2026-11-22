# Bybit Trading Bot — research repository

Частный репозиторий для исследования стратегий, воспроизводимых бэктестов и аудита модели исполнения. Это исследовательский проект, а не готовый live-бот.

## Статус и безопасность

- `Bybit_Trading_Engine_v14_research.py` — симулятор/бэктест-движок; сам по себе не отправляет ордера на Bybit.
- `bybit_downloader.py` получает публичные рыночные данные; API-ключи для описанных публичных endpoint не нужны.
- Результаты на Binance CryptoFetch OHLCV — рыночный proxy, а не Bybit execution replay.
- Не добавляйте реальные ключи, токены, пароли, приватные журналы и runtime state. Не включайте live-торговлю в рамках исследовательской настройки.

## Структура репозитория

```text
.
├── .github/workflows/       # CI
├── data/archives/cryptofetch # исходные ZIP-архивы (не редактировать)
├── dashboards/
│   ├── v15/                 # HTML/PDF dashboard и инструкция
│   └── quant_analytics/     # v2.2/v2.3 single-file и составные legacy HTML/JS
├── docs/
│   ├── audits/              # аудиты и большие исследовательские заметки
│   ├── reports/             # отчёты бэктестов и диагностики
│   └── operations/          # вспомогательные инструкции
├── research/legacy_tools/   # экспериментальные standalone-прототипы и архивные утилиты
├── scripts/                 # запуск бэктестов и утилиты
├── tests/                   # регрессионные тесты
├── Bybit_Trading_Engine_v14_research.py
├── bybit_downloader.py
├── .env.example
└── README.md
```

Движок и downloader оставлены в корне намеренно: существующие исследовательские скрипты импортируют их напрямую. Корневые HTML-дашборды перемещены в `dashboards/quant_analytics/`, когнитивный протокол — в `docs/operations/`, а старый validator — в `research/legacy_tools/`. Исходное содержимое файлов не менялось. Validator — архивный прототип: он ссылается на отсутствующие модули `Bybit_Trading_Engine_v14_Audit_Fixed`, `quant_math_lab_v16` и `quant_pattern_miner_v16`, поэтому не считается запускаемым инструментом проекта. ZIP-архивы сохранены без изменения содержимого.

## Документация

- [Навигация по документации](docs/README_RU.md)
- [Карта репозитория](docs/REPOSITORY_MAP_RU.md)
- [Исторические отчёты](docs/reports/)
- [Аудиты](docs/audits/)
- [Исходные гипотезы пользователя](docs/research/hypothesis_inputs/Гипотезы_пользователя_2026-11-03.md)
- [Аудит Quant Lab и очередь гипотез](docs/research/QUANT_LAB_ASSETS_AND_HYPOTHESIS_QUEUE_2026-11-03_RU.md)

## Быстрый старт

Python 3.10+ рекомендуется. Downloader требует `requests`.

```bash
python -m venv .venv
# Windows: .venv\\Scripts\\activate
# macOS/Linux:
source .venv/bin/activate
python -m pip install requests
```

Загрузка публичных данных, например:

```bash
python bybit_downloader.py --symbols BTCUSDT ETHUSDT --intervals 60 --start 2026-01-01 --datasets all --out data
```

## Проверки

Технический CI выполняет только compilation, secret scan и регрессионные тесты. Исторические рыночные backtest/walk-forward отделены в workflow `Manual historical market backtests` и запускаются только вручную через GitHub Actions → Run workflow.

```bash
python tests/test_gap_execution.py
python tests/test_time_model.py
python scripts/scan_secrets.py .
```

Для повторного прогона загруженных ZIP-данных:

```bash
python scripts/run_uploaded_dataset_backtests.py
python scripts/run_h1_walk_forward.py
```

Скрипты ищут исходные архивы в `data/archives/cryptofetch/`. Результаты независимых single-symbol прогонов не следует трактовать как одну совместную портфельную кривую.

## Правила работы

1. Сохраняйте репозиторий приватным.
2. Проверяйте `git status --short` и staged diff перед каждым commit.
3. Не используйте `git add .` без проверки состава изменений.
4. Не удаляйте и не перезаписывайте исходные архивы, holdout-данные и исторические результаты.
5. После публикации секрета отзовите/замените его: `.gitignore` не очищает Git history.
6. Синтетические тесты проверяют механику кода, но не доказывают наличие торгового edge.

## Исследовательские ветки

Основная текущая линия H1/regime исследований находится в [research/market-regime-v1-main](https://github.com/nikitanecilli-bit/bybit-trading-bot/tree/research/market-regime-v1-main). Ветки имеют расходящуюся историю; не выполнять массовый merge/rebase/force-push ради косметической уборки. Подробная карта веток и решений находится в Research Hub и документации исследовательской ветки.
