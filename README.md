# Projeto: Azure Cognitive Search

## Recursos Azure Necessários

A solução que você criará para a Fourth Coffee requer os seguintes recursos em sua assinatura Azure:

1. **Recurso Azure AI Search:** Gerenciará a indexação e consulta.
2. **Recurso Azure AI Services:** Fornece serviços de IA para enriquecer os dados na fonte de dados com insights gerados por IA.

*Observação: Seus recursos Azure AI Search e Azure AI Services devem estar na mesma localização!*

3. **Conta de Armazenamento com Contêineres de Blob:** Armazenará documentos brutos e outras coleções de tabelas, objetos ou arquivos.

### Criar um Recurso Azure AI Search

1. Faça login no [portal do Azure](https://portal.azure.com/).

2. Clique no botão `+ Criar um recurso`, pesquise por `Azure AI Search` e crie um recurso Azure AI Search com as seguintes configurações:

   - **Assinatura:** Sua assinatura do Azure.
   - **Grupo de Recursos:** Selecione ou crie um grupo de recursos com um nome único.
   - **Nome do Serviço:** Um nome único.
   - **Localização:** Escolha qualquer região disponível.
   - **Nível de Preços:** Básico

3. Selecione `Revisar + criar`, e após ver a resposta `Validação Bem-sucedida`, selecione `Criar`.

4. Após a conclusão da implantação, selecione `Ir para recurso`. Na página de visão geral do Azure AI Search, você pode adicionar índices, importar dados e pesquisar índices criados.

### Criar um Recurso Azure AI Services

1. Volte à página inicial do [portal do Azure](https://portal.azure.com/). Clique no botão `+ Criar um recurso` e pesquise por `Azure AI services`. Selecione `criar um plano de serviços Azure AI`. Isso o levará a uma página para criar um recurso Azure AI services. Configure com as seguintes configurações:

   - **Assinatura:** Sua assinatura do Azure.
   - **Grupo de Recursos:** O mesmo grupo de recursos do seu recurso Azure AI Search.
   - **Região:** A mesma localização do seu recurso Azure AI Search.
   - **Nome:** Um nome único.
   - **Nível de Preços:** Padrão S0

2. Marque a caixa de seleção para confirmar que leu e entendeu todos os termos abaixo. Selecione `Revisar + criar`. Após ver a resposta `Validação Bem-sucedida`, selecione `Criar`.

3. Aguarde a conclusão da implantação e, em seguida, visualize os detalhes da implantação.

### Criar uma Conta de Armazenamento

1. Retorne à página inicial do [portal do Azure](https://portal.azure.com/) e selecione o botão `+ Criar um recurso`.

2. Pesquise por `Conta de Armazenamento` e crie uma conta de armazenamento com as seguintes configurações:

   - **Assinatura:** Sua assinatura do Azure.
   - **Grupo de Recursos:** O mesmo grupo de recursos dos recursos Azure AI Search e Azure AI Services.
   - **Nome da Conta de Armazenamento:** Um nome único.
   - **Localização:** Escolha qualquer local disponível.
   - **Desempenho:** Padrão.
   - **Redundância:** Armazenamento Redundante Local (LRS).

3. Clique em `Revisar` e, em seguida, em `Criar`. Aguarde a conclusão da implantação e vá para o recurso implantado.

4. Na conta de armazenamento Azure que você criou, no painel de menu à esquerda, selecione `Configuração` (em Configurações).

5. Altere a configuração para `Permitir acesso anônimo a blobs` para `Habilitado` e selecione `Salvar`.

## Upload de Documentos no Azure Storage:

1. No painel de menu à esquerda, selecione Containers.

2. Clique em **+ Container**. Uma janela à direita será aberta.

3. Insira as seguintes configurações e clique em **Criar**:
- **Nome**: coffee-reviews
- **Nível de acesso público**: Container (acesso anônimo de leitura para contêineres e blobs)
- **Avançado**: sem alterações.

4. Em uma nova guia do navegador, faça o download dos arquivos compactados de avaliações de café em https://aka.ms/mslearn-coffee-reviews e extraia os arquivos para a pasta "reviews".

5. No portal do Azure, selecione o seu contêiner coffee-reviews. No contêiner, selecione **Upload**.

6. Na janela de upload de blob, selecione **Selecionar um arquivo**.

7. Na janela do Explorador, selecione todos os arquivos na pasta "reviews", clique em **Abrir** e, em seguida, clique em **Upload**.

8. Após o upload ser concluído, você pode fechar a janela de upload de blob. Seus documentos agora estão no contêiner de armazenamento coffee-reviews.

## Indexação de Documentos no Azure AI Search:

1. No portal do Azure, acesse o seu recurso Azure AI Search. Na página de Visão geral, selecione Importar dados.


2. Na página Conectar aos seus dados, na lista de Fonte de dados, selecione Armazenamento de Blobs do Azure. Complete os detalhes da fonte de dados com os seguintes valores:

- Fonte de dados: Armazenamento de Blobs do Azure
- Nome da fonte de dados: coffee-customer-data
- Dados para extrair: Conteúdo e metadados
- Modo de análise: Padrão
- String de conexão: *Selecione Escolher uma conexão existente. Escolha sua conta de armazenamento, selecione o contêiner coffee-reviews e clique em Selecionar.
- Autenticação de identidade gerenciada: Nenhum
- Nome do contêiner: este campo é preenchido automaticamente após a escolha de uma conexão existente.
- Pasta Blob: Deixe em branco.
- Descrição: Avaliações para cafeterias Fourth Coffee.

3. Selecione Próximo: Adicionar habilidades cognitivas (Opcional).

4. Na seção Anexar Serviços Cognitivos, selecione seu recurso Azure AI services.

5. Na seção Adicionar enriquecimentos:

- Altere o nome da habilidade para coffee-skillset.
- Selecione a caixa de seleção Habilitar OCR e mesclar todo o texto no campo merged_content.
- Nota: É importante selecionar Habilitar OCR para ver todas as opções de campos enriquecidos.

6. Garanta que o campo Fonte de dados esteja definido como merged_content.

- Altere o nível de granularidade do enriquecimento para Páginas (5000 caracteres por bloco).
- Não selecione Habilitar enriquecimento incremental.
- Selecione os seguintes campos enriquecidos:

|**Habilidade Cognitiva**	| **Parâmetro** | **Nome do Campo**|
| :---:|-----------| :---:|
|Extrair nomes de locais	|	| locations |
|Extrair frases-chave	|	| keyphrases|
|Detectar sentimento	|	| sentiment|
|Gerar tags de imagens	|	| imageTags|
|Gerar legendas de imagens	|	| imageCaption|

- Sob Salvar enriquecimentos em um repositório de conhecimento, selecione:
    - Projeções de Imagem
    - Documentos
    - Páginas
    - Frases-chave
    - Entidades
    - Detalhes da imagem
    - Referências de imagem
- Nota: Se aparecer um aviso solicitando uma String de Conexão da Conta de Armazenamento.

7. Selecione Escolher uma conexão existente. Escolha a conta de armazenamento criada anteriormente.

- Clique em + Contêiner para criar um novo contêiner chamado knowledge-store com o nível de privacidade definido como Privado, e selecione Criar.
- Selecione o contêiner knowledge-store e, em seguida, clique em Selecionar na parte inferior da tela.
- Selecione Projeções de Blob Azure: Documento. Uma configuração para Nome do Contêiner com o contêiner knowledge-store preenchido automaticamente será exibida. Não altere o nome do contêiner.

8. Selecione Próximo: Personalizar índice de destino. Altere o nome do índice para coffee-index.

- Garanta que a Chave esteja definida como metadata_storage_path. Deixe o nome do Sugestor em branco e o Modo de Pesquisa será preenchido automaticamente.

9. Revise as configurações padrão dos campos do índice. Selecione Filtrável para todos os campos que já estão selecionados por padrão.

10. Selecione Próximo: Criar um indexador.

11. Altere o nome do Indexador para coffee-indexer.

- Deixe o Agendamento definido como Uma vez.
- Expanda as opções Avançadas. Garanta que a opção Codificar Chaves em Base-64 esteja selecionada, pois a codificação de chaves pode tornar o índice mais eficiente.

12. Selecione Enviar para criar a fonte de dados, conjunto de habilidades, índice e indexador. O indexador é executado automaticamente e executa o pipeline de indexação, que:

- Extrai os campos de metadados e conteúdo do documento da fonte de dados.
- Executa o conjunto de habilidades de habilidades cognitivas para gerar mais campos enriquecidos.
- Mapeia os campos extraídos para o índice.

13. Retorne à página do seu recurso Azure AI Search. No painel esquerdo, em Gerenciamento de Pesquisa, selecione Indexadores. Selecione o indexador recém-criado coffee-indexer. Aguarde um minuto e selecione ↻ Atualizar até que o Status indique sucesso.

14. Selecione o nome do indexador para ver mais detalhes.

## Consulta ao Índice no Azure AI Search:

1. Utilize o Search explorer para escrever e testar consultas. O Search explorer é uma ferramenta integrada no portal do Azure que fornece uma maneira fácil de validar a qualidade do seu índice de pesquisa. Você pode usar o Search explorer para escrever consultas e revisar os resultados em JSON.

- Na página Visão geral do seu serviço de pesquisa, selecione Search explorer no topo da tela.
- Observe como o índice selecionado é o coffee-index que você criou. Abaixo do índice selecionado, altere a visualização para a visão JSON.
- No campo do editor de consulta JSON, copie e cole o seguinte código:
~~~json
{
    "search": "*",
    "count": true
}
~~~
- Selecione Search. A consulta de pesquisa retorna todos os documentos no índice de pesquisa, incluindo uma contagem de todos os documentos no campo @odata.count. O índice de pesquisa deve retornar um documento JSON contendo seus resultados de pesquisa.

2. Agora, vamos filtrar por localização. No campo do editor de consulta JSON, copie e cole o seguinte código:

~~~json
{
    "search": "locations:'Chicago'",
    "count": true
}
~~~
- Selecione Search. A consulta pesquisa todos os documentos no índice e filtra as avaliações com uma localização em Chicago. Você deve ver 3 no campo ***@odata.count***.

3. Agora, vamos filtrar por sentimento. No campo do editor de consulta JSON, copie e cole o seguinte código:

~~~json
{
    "search": "sentiment:'negative'",
    "count": true
}
~~~
- Selecione Search. A consulta pesquisa todos os documentos no índice e filtra as avaliações com um sentimento negativo. Você deve ver 1 no campo ***@odata.count***.

4. Observe como os resultados são classificados por ***@search.score***. Este é o escore atribuído pelo mecanismo de pesquisa para mostrar o quão bem os resultados correspondem à consulta dada.

5. Um dos problemas que podemos querer resolver é o motivo de certas avaliações. Vamos dar uma olhada nas palavras-chave associadas à avaliação negativa.

## Revisar o Knowledge Store no Azure:

1. No portal do Azure, retorne à sua conta de armazenamento do Azure.

- No painel de menu à esquerda, selecione Containers. Escolha o container knowledge-store.

2. Selecione qualquer um dos itens e clique no arquivo objectprojection.json.

3. Selecione Edit para visualizar o JSON gerado para um dos documentos do seu armazenamento de dados do Azure.

4. Selecione o caminho de navegação de blob de armazenamento no canto superior esquerdo da tela para retornar aos Containers da conta de armazenamento.

5. Nos Containers, escolha o container coffee-skillset-image-projection. Selecione qualquer um dos itens.

6. Selecione qualquer um dos arquivos .jpg. Clique em Edit para ver a imagem armazenada do documento. Observe como todas as imagens dos documentos são armazenadas dessa maneira.

7. Selecione o caminho de navegação de blob de armazenamento no canto superior esquerdo da tela para retornar aos Containers da conta de armazenamento.

8. Selecione o Storage browser no painel à esquerda e escolha Tables. Há uma tabela para cada entidade no índice. Selecione a tabela coffeeSkillsetKeyPhrases.

9. Analise as palavras-chave que o Knowledge Store conseguiu capturar do conteúdo nas avaliações. Muitos dos campos são chaves, permitindo vincular as tabelas como um banco de dados relacional. O último campo mostra as palavras-chave que foram extraídas pelo conjunto de habilidades.






