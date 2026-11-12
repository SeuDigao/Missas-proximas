---
name: missas-proximas
description: Descobre os horários das próximas missas católicas perto da pessoa ou em outro lugar que ela indicar (cidade ou bairro). Por padrão usa a localização aproximada pelo IP; se a pessoa citar uma cidade, bairro ou endereço, usa esse lugar no lugar da localização. Obtém a data/hora atuais, encontra igrejas, paróquias, matrizes, capelas e santuários católicos num raio (padrão 5 km), localiza as redes sociais e sites de cada uma e informa quais missas acontecem a seguir, com distância. Use sempre que a pessoa perguntar sobre missa, horário de missa, "missa hoje", "missa perto de mim", "onde tem missa", "igreja católica por aqui", capela, paróquia, celebração ou culto católico, também para "missa em [cidade]" ou "missa no bairro [X] de [cidade]", mesmo sem dizer "skill" ou citar a localização.
allowed-tools: Bash WebSearch WebFetch
---

# Missas próximas

Objetivo: dizer à pessoa quais são as próximas missas católicas perto dela, com horário, local e distância, usando fontes que ela possa conferir.

Responda no idioma da pessoa (normalmente português do Brasil). A resposta aparece em tela de celular: seja curto e direto.

## Parâmetros

- **Local**: a localização aproximada pelo IP, a menos que a pessoa cite outro lugar (cidade, bairro, endereço ou igreja específica). Citou lugar, vale o lugar citado.
- **Raio**: 5 km por padrão. Se a pessoa pedir outro ("até 10 km", "só aqui no bairro"); o raio vale a partir do ponto de referência do local escolhido, use o pedido.
- **Janela**: a partir de agora até o fim do dia seguinte. Se a pessoa pedir "missa de domingo" ou outro dia, use esse dia.

## Passo 1: ponto de referência e momento atual

Primeiro decida de onde medir (o **ponto de referência**):

**A. A pessoa não citou lugar** ("missa perto de mim", "tem missa hoje?"):

Faça as duas chamadas em paralelo numa única mensagem:

- **Hora atual**: use `Bash` com o comando `date` para obter o dia da semana, data e hora atuais.
- **Localização por IP**: use `WebFetch` na URL `https://ipapi.co/json/` — o resultado é um JSON com `city`, `region`, `country_name`, `latitude` e `longitude`. Use esses valores como ponto de referência.

Avise a pessoa que a localização é aproximada (baseada no IP, não em GPS) e confirme a cidade encontrada antes de prosseguir.

**B. A pessoa citou cidade, bairro, endereço ou igreja** ("missa em Gramado", "missa no bairro Moinhos de Vento, Porto Alegre"):

- **Hora atual**: use `Bash` com o comando `date`.
- **Coordenadas do lugar**: use `WebSearch` com "[bairro ou cidade], [UF] coordenadas latitude longitude" e depois `WebFetch` na página de resultado mais útil (Wikipedia, Google Maps embed, ou similar) para extrair lat/lng. Alternativamente busque diretamente "[lugar] coordinates" e leia o resultado.

- Cidade sem bairro: o ponto de referência é o centro da cidade. Avise que as distâncias são a partir do centro.
- Bairro: use o centro do bairro e avise na resposta.
- Se o nome for ambíguo e o contexto não resolver, pergunte a cidade/UF em uma frase.
- Se a pessoa usar "aqui" ou "onde estou", é o caso A.

Em ambos os casos:

- Use os dados de `date` para o dia da semana, a hora e a data.
- Se a localização por IP (caso A) falhar ou retornar cidade incorreta, peça o bairro ou a cidade em uma frase e siga como no caso B.
- Não grave o local em memória; ele vale só para este pedido.

## Passo 2: encontrar igrejas e capelas

Use `WebSearch` com 2 a 4 consultas em paralelo, sempre incluindo a cidade/bairro:

- "igreja católica [cidade] horário missa"
- "paróquia [cidade] missas"
- "capela católica [bairro] [cidade]"
- "santuário OR matriz [cidade] missa" (só se o raio for grande ou as anteriores trouxerem pouco)

Para cada resultado:

1. Calcule a distância aproximada até o ponto de referência usando a fórmula de Haversine se tiver coordenadas, ou estime pela distância entre bairros/cidades. Descarte resultados claramente fora do raio.
2. Descarte o que não é católico (igrejas evangélicas, centros espíritas, etc.).
3. Junte duplicatas (mesmo lugar aparecendo em consultas diferentes).
4. Distinga **paróquia/matriz** de **capela/comunidade**: capelas geralmente têm horários publicados na página da paróquia ou da diocese a que pertencem.

Se a pessoa estiver em área rural ou cidade pequena, é normal haver poucas igrejas com informação online. Se vier muito pouco dentro do raio, diga isso e ofereça ampliar.

## Passo 3: redes sociais e horários

Para cada igreja/capela do raio (comece pelas mais próximas e pelas paróquias), use `WebSearch` e `WebFetch`:

Pesquisas com `WebSearch`:
- "[nome da paróquia] [cidade] horário das missas"
- "[nome da paróquia] [cidade] instagram facebook"

Fontes, da mais para a menos confiável:

1. Site oficial da paróquia ou da diocese/arquidiocese.
2. Página pública da paróquia no Facebook ou Instagram, ou site/blog dela.
3. Horário listado no Google Maps (costuma ser de funcionamento, não de missa; use só como pista).
4. Agregadores de horários de missa, com cautela.

Use `WebFetch` nas páginas encontradas. Instagram e Facebook muitas vezes exigem login e não abrem; se isso acontecer, diga que o perfil existe mas não foi possível ler, e use o que aparecer nos resultados da busca. Nunca finja ter lido uma rede social que não abriu.

Registre para cada lugar: nome, tipo (paróquia/capela), perfis/sites encontrados, horários semanais de missa, e **de onde veio e quão recente é** a informação.

## Passo 4: calcular as próximas missas

1. Monte a lista de todas as missas (dia da semana + horário) de cada local.
2. Filtre as que acontecem depois da hora atual (obtida no Passo 1), dentro da janela.
3. Ordene por horário (e, em empate, por distância).
4. Considere o que muda o horário: feriados e datas litúrgicas (Natal, Semana Santa, Todos os Santos, Finados, festa do padroeiro), avisos de "sem missa" e horário de verão/inverno da paróquia. Se a data for especial ou a fonte tiver aviso, mencione.

Regras de honestidade, porque a pessoa vai se deslocar com base na resposta:

- Nunca invente horário. Se não achou, diga "não encontrei o horário" e dê o contato ou a rede social para conferir.
- Marque cada missa com o grau de confiança: **confirmado** (fonte oficial recente), **a confirmar** (post antigo, fonte indireta ou sem data).
- Se a fonte for antiga (mais de uns 6 meses) ou sem data, trate como "a confirmar".
- Recomende confirmar por telefone ou rede social quando a missa for em menos de 1 hora ou a fonte for fraca.

## Formato da resposta

Se o lugar não for a localização do aparelho, a primeira linha diz o ponto de referência (ex.: "Missas perto do centro de Gramado, RS").

Comece pela resposta: a próxima missa. Depois a lista curta, no máximo uns 5 a 6 itens, e só então detalhes.

```
Próxima missa: 18h30 (daqui a 40 min), Capela São José, 1,2 km.

Hoje (terça, 3/11):
• 18h30, Capela São José (capela), 1,2 km, confirmado
• 19h30, Matriz N. Sra. de Fátima (paróquia), 2,8 km, confirmado
• 19h00, Capela Santo Antônio, 4,1 km, a confirmar

Amanhã:
• 7h00, Matriz N. Sra. de Fátima, 2,8 km

Redes: Matriz N. Sra. de Fátima: @paroquiafatima (Instagram) | facebook.com/...
```

Notas de formato:
- Horários no estilo brasileiro ("18h30").
- Distância em km com uma casa decimal (estimada se não houver coordenadas exatas).
- Inclua os perfis/sites encontrados para os locais listados, para a pessoa conferir.
- Termine com uma única oferta útil: ampliar o raio, ver outro dia, ou endereço da igreja para traçar rota.
- Não faça perguntas antes de tentar: busque primeiro, pergunte só se faltar algo essencial.

## Casos especiais

- **Nenhuma missa na janela**: diga qual é a próxima (ex.: "amanhã às 7h") em vez de só dizer que não há.
- **Cidade pequena, bairro sem igreja ou lugar sem informação online**: diga isso e amplie o raio ou inclua a cidade inteira, deixando claro o que foi feito.
- **Pessoa pede um lugar e depois diz "e aqui perto?"**: volte ao caso A.
- **Pedido para um dia futuro em outra cidade** (viagem): o mesmo método; use o dia pedido.
- **Pedido de confissão, adoração ou terço**: o mesmo método serve; busque esses horários nas mesmas fontes.
- **Missas em outros ritos ou idiomas**: inclua se a pessoa pedir.
