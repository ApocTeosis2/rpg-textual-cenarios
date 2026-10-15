# Aventuras para o RPG textual

Este repositório contém **somente conteúdo de aventuras**. O motor Flask/PythonAnywhere lê `scenarios/index.json`, apresenta o catálogo e carrega o JSON escolhido de `scenarios/<id>/scenario.json`. O motor não precisa de código específico para cada história.

## Estrutura

```text
scenarios/
  index.json
  <scenario_id>/
    scenario.json
```

O catálogo aponta cada ID para um arquivo. Para adicionar uma história, crie um novo diretório e um JSON com `schema_version: 2`, depois inclua uma entrada no índice. Não altere o motor para adicionar locais, condições, ações ou texto narrativo.

## Formato de uma aventura

- `scenario_id`, `title`, `summary`, `start_location` e `start_text` identificam a aventura.
- `initial_state` fornece os campos/valores da partida; `stats` determina os indicadores exibidos.
- `locations` associa cada local a nome, descrição e ações disponíveis naquele local.
- Uma ação pode declarar `when` (condições que a desbloqueiam), `time_cost` e `effects`.
- Operações de efeito: `set`, `add`, `add_clamped`, `move`, `log`, `if` e `roll`.
- Condições usam `field`, `op` (`eq`, `ne`, `gt`, `gte`, `lt`, `lte`, `truthy`, `falsy`) e, quando aplicável, `value`.

Exemplo de ação:

```json
{
  "id": "abrir_portao",
  "label": "Abrir o portão",
  "time_cost": 5,
  "when": [{"field": "chave_encontrada", "op": "eq", "value": true}],
  "effects": [
    {"op": "move", "location": "jardim"},
    {"op": "log", "text": "O portão range e se abre."}
  ]
}
```

## Fonte e atualização

O PythonAnywhere consulta o conteúdo público em `https://raw.githubusercontent.com/ApocTeosis2/rpg-textual-cenarios/main/`. Pode-se sobrescrever `SCENARIO_GITHUB_OWNER`, `SCENARIO_GITHUB_REPO` e `SCENARIO_GITHUB_BRANCH` no ambiente. O aplicativo mantém cache em memória por 5 minutos e cache de disco, além de cópias locais para continuar disponível se o GitHub estiver temporariamente indisponível.

A aplicação já existente usa um motor e uma base SQLite compartilhados; cada nova partida guarda seu `scenario_id`, então histórias diferentes podem coexistir sem misturar o estado das partidas.
