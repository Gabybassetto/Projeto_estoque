# Roteiro de apresentação do Stockly

Sugestão de apresentação narrada pelos integrantes, com duração aproximada de cinco minutos.

## 0:00–0:30 | Apresentação

“Olá! Este é o Stockly, um sistema de controle de estoque desenvolvido como projeto acadêmico de Desenvolvimento Front-End. Com ele, podemos cadastrar produtos, acompanhar quantidades e identificar o que precisa ser reposto.”

## 0:30–1:10 | Painel e indicadores

Mostrar os quatro indicadores do painel: produtos cadastrados, unidades disponíveis, estoque baixo e produtos sem estoque. Explicar que os valores acompanham os produtos da lista. Apresentar o menu lateral e os atalhos para visualizar os itens que precisam de reposição.

## 1:10–2:10 | Cadastro e manutenção dos produtos

Cadastrar um produto de exemplo, como uma lata de tinta, e apresentar os campos de nome, SKU, categoria, quantidade, quantidade mínima e preço. Demonstrar a validação de campos e de SKU duplicado. Salvar, editar o produto e ajustar sua quantidade pelos controles da tabela. Em seguida, pesquisar pelo nome ou SKU e aplicar os filtros de categoria e situação. Se houver tempo, mostrar a confirmação antes de excluir um item.

## 2:10–2:45 | Salvamento das alterações

Alterar uma quantidade e explicar que os dados dos produtos são salvos automaticamente no armazenamento local do navegador. Recarregar a página para confirmar que o cadastro e as alterações continuam disponíveis no mesmo navegador e dispositivo. Esclarecer que os dados não são sincronizados com outros dispositivos.

## 2:45–3:40 | Componentes da aplicação

Mostrar como o `App.vue` coordena os dados e importa `AppHeader.vue`, `SidebarMenu.vue`, `StatCard.vue`, `ProductTable.vue` e `ProductForm.vue`. Apontar os quatro cartões criados com o mesmo `StatCard.vue`, cada um recebendo informações diferentes, e mostrar a tabela e o formulário como componentes separados.

## 3:40–4:30 | Recursos do Vue

No `StatCard.vue`, mostrar `defineProps()` recebendo título, valor e tipo visual; no `App.vue`, apontar os dados enviados ao componente. Explicar como `v-for` renderiza cartões e produtos, como `v-if` controla a exibição do formulário e como `ref()` e `computed()` mantêm os dados e indicadores atualizados. Mostrar o `watch()` que grava as alterações dos produtos no armazenamento local.

## 4:30–5:00 | Estilo e encerramento

Mostrar `src/assets/styles.css` e o layout responsivo feito com CSS próprio. Encerrar resumindo que o Stockly oferece cadastro e acompanhamento de produtos, indicadores de estoque e salvamento local das alterações.
