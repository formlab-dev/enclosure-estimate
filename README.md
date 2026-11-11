# Formlab: оценка стоимости корпуса для электроники

Инструмент для ИИ-ассистентов (MCP) и обычный HTTP API. Считает ориентировочную стоимость и сроки разработки и производства пластикового корпуса для электронного прибора: промдизайн, конструкторская документация, макет, литьё полиуретана в силиконовые формы, литьё пластика в алюминиевые и стальные пресс-формы.

Расчёт опирается на 15 реальных корпусов, которые проектное бюро [Формлаб](https://formlab.ru) разработало и запустило в производство. Методика описана в статье [Калькулятор производства пластиковых корпусов для приборов](https://formlab.ru/articles/705-kalkulyator-proizvodstva-plastikovykh-korpusov-dlya-priborov). Цены даны в рублях по курсу ЦБ, без НДС и доставки. Это оценка, а не оферта. Точный расчёт под ваше устройство можно бесплатно получить на [formlab.ru/contacts](https://formlab.ru/contacts).

*English: an MCP tool and HTTP API that estimates the cost and lead time of designing and manufacturing a custom plastic enclosure for an electronic device in Russia. The estimate is based on 15 real enclosures by Formlab (formlab.ru), with runs from 1 to 200,000 pcs.*

## Подключение

**MCP (Streamable HTTP):** `https://formlab.ru/mcp`. Авторизация не нужна.

Claude Code:

```bash
claude mcp add --transport http formlab https://formlab.ru/mcp
```

Плагин Claude Code ставит MCP-сервер и skill одной командой:

```bash
/plugin marketplace add formlab-dev/enclosure-estimate
/plugin install formlab-enclosure-estimate@formlab
```

Claude Desktop и claude.ai: Settings → Connectors → Add custom connector, URL `https://formlab.ru/mcp`.

Cursor, Windsurf и другие клиенты с поддержкой удалённых MCP (`mcp.json`):

```json
{
  "mcpServers": {
    "formlab": { "url": "https://formlab.ru/mcp" }
  }
}
```

ChatGPT (GPT Actions): схема OpenAPI лежит по адресу `https://formlab.ru/api/estimate/openapi.json`.

## Инструмент `estimate_plastic_enclosure`

| Параметр | Тип | Обязательный | Описание |
|---|---|---|---|
| `length_mm` | число | да | Длина корпуса, мм (5-3000). Если известна только плата, укажите её размер плюс 10 мм |
| `width_mm` | число | нет | Ширина, мм |
| `height_mm` | число | нет | Высота, мм |
| `quantity` | целое | нет | Тираж, шт (1-200 000), по умолчанию 1000 |
| `design` | строка | нет | `copy` - копия существующего корпуса, `improve` - переработка внешнего вида, `original` - свой дизайн (по умолчанию) |

Ответ содержит:

- эталонный корпус из базы;
- стоимость разработки в трёх вариантах;
- стоимость макета;
- подходящие под тираж технологии с ценой оснастки, ценой штуки и сроками;
- контакт для точного расчёта.

## HTTP API

```
GET https://formlab.ru/api/estimate?length_mm=120&width_mm=80&height_mm=30&quantity=500
```

Ответ приходит в формате JSON. Ограничение: несколько запросов в секунду с одного IP, при превышении сервер отвечает 429.

## Состав репозитория

- `skills/formlab-enclosure-estimate/SKILL.md` - skill для Claude: когда вызывать инструмент и как объяснять результат;
- `.claude-plugin/` - плагин и маркетплейс для Claude Code;
- `.mcp.json` - подключение удалённого MCP-сервера;
- `server.json` - карточка для официального реестра MCP.

Сам сервис работает на стороне Формлаба, его код и данные в репозиторий не входят.

## Контакты

Формлаб - проектное бюро по разработке и производству корпусов для электроники, работаем с 2009 года.
[formlab.ru](https://formlab.ru) · +7 926 904 66 66 · hello@formlab.pro

Уязвимости и проблемы безопасности: см. [SECURITY.md](SECURITY.md).
