# Aventuras para o RPG textual

Este repositório contém **conteúdo de aventuras**, organizado em diretórios. O aplicativo no PythonAnywhere carrega `scenarios/index.json` e, para cada aventura selecionada, lê `scenarios/<id>/scenario.json`. O motor permanece compartilhado; cada arquivo de cenário descreve seu próprio mapa, estado, ações e relações.

## Estrutura do repositório

```text
scenarios/
  index.json
  ponte_velha/
    scenario.json
  luzes_do_salgueiral/
    scenario.json
  <outro_id>/
    scenario.json
```

Para acrescentar uma aventura, crie seu próprio diretório e JSON, depois registre o ID e caminho no índice. Não é preciso copiar o número de locais, ações ou campos de estado de outra história.

## Estrutura de cada cenário

Cada JSON usa o envelope `schema_version: 2` e os campos obrigatórios `scenario_id`, `title`, `start_location`, `initial_state` e `locations`. `summary`, `start_text` e `stats` são opcionais.

- `initial_state` pode ter campos diferentes em cada aventura.
- `locations` pode ter qualquer quantidade de lugares; cada lugar lista suas próprias ações.
- Cada ação pode ter condições (`when`), custo de tempo e efeitos.
- `move` conecta locais. As conexões podem formar redes, ramos, retornos e convergências diferentes.
- `stats` escolhe quais campos do estado aparecem na interface.

As duas aventuras atuais já exercitam topologias diferentes: `ponte_velha` tem 8 locais e converge pistas de bosque, capela e moinho; `luzes_do_salgueiral` tem 10 locais, com ramos próprios para mercado, observatório, bosque, ermida, torre e cais submerso. Elas também usam campos de estado e relações de desbloqueio diferentes.

## Regras declarativas disponíveis no motor atual

Condições aceitas: `eq`, `ne`, `gt`, `gte`, `lt`, `lte`, `truthy` e `falsy`.

Operações de efeito aceitas: `set`, `add`, `add_clamped`, `move`, `log`, `if` e `roll`.

Exemplo de ação que abre uma passagem apenas quando três pistas já foram obtidas:

```json
{
  "id": "abrir_passagem",
  "label": "Abrir a passagem oculta",
  "time_cost": 10,
  "when": [
    {"field": "chave", "op": "eq", "value": true},
    {"field": "mapa_lido", "op": "eq", "value": true},
    {"field": "correnteza_medida", "op": "eq", "value": true}
  ],
  "effects": [
    {"op": "move", "location": "câmara"},
    {"op": "log", "text": "A porta se abre."}
  ]
}
```

## Limite de extensibilidade

Os cenários podem variar em topologia, número de elementos, nomes, textos, estado e combinações de regras **desde que sejam expressos com o esquema e as operações acima**. Um tipo de mecânica novo — por exemplo, combate com iniciativa, inventário de itens individuais ou diálogo com escolhas em árvore — exige primeiro ampliar o DSL e o motor; não basta adicionar um diretório.

## Fonte e atualização

O PythonAnywhere consulta o conteúdo público em `https://raw.githubusercontent.com/ApocTeosis2/rpg-textual-cenarios/main/`. Pode-se sobrescrever `SCENARIO_GITHUB_OWNER`, `SCENARIO_GITHUB_REPO` e `SCENARIO_GITHUB_BRANCH` no ambiente. O aplicativo mantém cache em memória por 5 minutos e cache em disco para tolerar falhas temporárias da rede.

A aplicação usa uma base SQLite compartilhada para persistir partidas e ações. Cada partida guarda seu próprio `scenario_id`, mantendo o conteúdo do cenário separado dos dados de progresso.
