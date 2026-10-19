
UNIVERSIDADE ESTADUAL DA PARAÍBA
CENTRO DE CIÊNCIAS E TECNOLOGIA
ANÁLISE E DESENVOLVIMENTO DE SISTEMAS
PROJETO INTEGRADOR E PRÁTICA PROFISSIONAL II
Professor: Thiago Soares Marques
GRUPO: Fernanda Ramos, Johnata Ruan, Anna Laura, Lucas Evaristo
 
SIGASUS – SISTEMA INTEGRADO DE GESTÃO E AGENDAMENTO
DO SUS
Modelagem de Casos de Uso
 
CAMPINA GRANDE
2026

SIGASUS
Sistema Integrado de Gestão e Agendamento do SUS
 
Pré-Projeto apresentado no curso de Análise e Desenvolvimento de Sistemas da Universidade Estadual da Paraíba. Professor: Thiago Soares Marques.
 
CAMPINA GRANDE
2026

1. Objetivo e delimitação do sistema
O SIGASUS permite que o próprio paciente cadastre seus dados, informe os sintomas e solicite uma consulta. O sistema processa automaticamente os sintomas por regras inspiradas nas categorias de cores do Protocolo de Manchester, calcula a prioridade e o agendamento, identifica a UBS de referência pelo CEP e apresenta um comprovante. Não existe atendente ou profissional de triagem operando a plataforma neste modelo.
A classificação automática é uma simulação acadêmica baseada em palavras-chave, não uma aplicação clínica validada do Protocolo de Manchester. Em uma implantação real, casos de emergência exigem orientação imediata para serviços de urgência e avaliação profissional; a ferramenta não substitui triagem clínica.
2. Atores e regras de negócio
Ator principal: Paciente — utiliza diretamente a plataforma para informar seus dados e sintomas, solicitar agendamento e visualizar o resultado e o comprovante. O algoritmo de classificação e as rotinas de agendamento são componentes internos do SIGASUS, não atores externos.
Regra	Descrição
RN01	Validar CPF com 11 dígitos e dígitos verificadores; recusar sequências repetidas.
RN02	Validar CNS com 15 dígitos, data real não futura e CEP com 8 dígitos; desconsiderar pontuação em CPF, CNS e CEP.
RN03	Normalizar os sintomas e classificá-los por listas de palavras-chave, priorizando a maior gravidade reconhecida: vermelho, laranja, amarelo, verde ou azul; sem correspondência, azul.
RN04	Definir prazos e horários por cor: vermelho mesmo dia 08h; laranja 1 dia 08h30; amarelo 3 dias 09h; verde 7 dias 10h; azul 15 dias 11h.
RN05	Identificar bairro e UBS a partir das faixas de CEP mapeadas de Campina Grande; se não houver correspondência, orientar confirmação na Secretaria de Saúde.
RN06	Exibir comprovante com dados pessoais, risco, prioridade, data, horário, UBS, bairro e equipe responsável; registrar histórico em comprovantes_sigasus.txt.
3. Tabela de rastreabilidade — requisitos, casos de uso e ator
ID	Requisito funcional	Caso(s) de uso	Responsável
RF01	Permitir cadastro direto do paciente com dados pessoais, CPF, CNS, nascimento, endereço e contato.	UC01	Paciente
RF02	Validar automaticamente CPF, CNS, data de nascimento e CEP antes de prosseguir.	UC01	Sistema SIGASUS
RF03	Permitir ao paciente solicitar agendamento de consulta.	UC02	Paciente
RF04	Receber do paciente a descrição dos sintomas.	UC03	Paciente
RF05	Classificar automaticamente o risco em cinco cores por regras de sintomas.	UC02 / UC04 (inclui IN01)	Sistema SIGASUS
RF06	Definir automaticamente prioridade, data e horário pela classificação.	UC02 / UC05 (inclui IN02)	Sistema SIGASUS
RF07	Identificar a UBS de referência a partir do CEP.	UC02 / UC05 (inclui IN03)	Sistema SIGASUS
RF08	Apresentar ao paciente a classificação e a prioridade atribuídas.	UC04	Paciente
RF09	Apresentar os dados do agendamento e da UBS indicada.	UC05	Paciente
RF10	Gerar, exibir e salvar comprovante e histórico de atendimento.	UC06 (inclui IN04 no fluxo UC02)	Sistema SIGASUS
Na coluna Responsável, Paciente identifica quem interage com a plataforma e Sistema SIGASUS identifica quem executa automaticamente a operação. O Sistema SIGASUS não é ator externo no diagrama UML.
IN01 a IN04 representam comportamentos internos incluídos no processo de solicitação, não ações de um profissional externo. RN01 a RN06 são regras de negócio, não casos de uso independentes.
4. Diagrama de casos de uso UML
Figura 1 — Ator Paciente, objetivos de interação e comportamentos internos incluídos no processo de agendamento.

O arquivo editável diagrama_casos_uso.svg e o arquivo-fonte diagrama_casos_uso.dot acompanham esta entrega; o PNG é a exportação solicitada.
5. Especificações descritivas dos casos de uso
UC01 — Cadastrar dados pessoais
Ator principal: Paciente
Objetivo: Registrar dados necessários ao atendimento.
Pré-condição: Paciente inicia cadastro na plataforma.
Pós-condição: Dados aceitos para as etapas seguintes.
Fluxo principal:
1. Paciente informa nome, CPF, CNS, nascimento, endereço, CEP e contato.
2. Sistema remove pontuação quando cabível e valida CPF, CNS, data e CEP.
3. Sistema aceita os dados e habilita a solicitação de consulta.
Fluxos alternativos e exceções:
A1 — CPF inválido (dígitos verificadores incorretos ou sequência repetida): o sistema recusa o campo e solicita correção.
A2 — CNS, data ou CEP inválidos: o sistema indica o campo e solicita nova digitação.
UC02 — Solicitar agendamento de consulta
Ator principal: Paciente
Objetivo: Concluir a solicitação de atendimento com classificação e agendamento automáticos.
Pré-condição: Dados cadastrais válidos e sintomas informados.
Pós-condição: Consulta calculada e comprovante preparado conforme as regras do protótipo.
Fluxo principal:
1. Paciente inicia a solicitação após o cadastro.
2. Sistema obtém os sintomas informados em UC03.
3. Sistema executa classificação automática IN01.
4. Sistema calcula prioridade, data e horário em IN02.
5. Sistema localiza UBS de referência em IN03.
6. Sistema gera e registra o comprovante em IN04.
7. Paciente visualiza os dados da solicitação.
Fluxos alternativos e exceções:
A1 — CEP sem faixa cadastrada: sistema orienta confirmar UBS na Secretaria de Saúde.
A2 — Sinais de emergência: o protótipo aplica a cor vermelha; para uso real, deve orientar atendimento de urgência imediato, sem depender do agendamento.
UC03 — Informar sintomas
Ator principal: Paciente
Objetivo: Fornecer descrição textual dos sintomas para análise automática.
Pré-condição: Cadastro válido.
Pós-condição: Sintomas disponíveis para o algoritmo.
Fluxo principal:
1. Paciente descreve sintomas e condições atuais.
2. Sistema normaliza texto (acentos e letras).
3. Sistema encaminha descrição para classificação interna.
Fluxos alternativos e exceções:
A1 — Nenhuma palavra-chave reconhecida: algoritmo classifica como azul, conforme a regra descrita no pré-projeto; essa regra não comprova ausência de risco.
UC04 — Consultar resultado da classificação
Ator principal: Paciente
Objetivo: Conhecer a cor e a prioridade calculadas.
Pré-condição: Sintomas processados pelo algoritmo.
Pós-condição: Resultado apresentado ao paciente.
Fluxo principal:
1. Sistema identifica a cor de maior gravidade entre as correspondências.
2. Sistema apresenta cor e descrição da prioridade.
3. Paciente visualiza o resultado.
Fluxos alternativos e exceções:
A1 — Ausência de correspondência: exibe azul, conforme regra do protótipo.
A2 — Mais de uma cor encontrada: prevalece a mais grave.
UC05 — Consultar dados do agendamento
Ator principal: Paciente
Objetivo: Verificar data, horário, bairro e UBS atribuídos.
Pré-condição: Classificação concluída e agendamento calculado.
Pós-condição: Informações de agendamento apresentadas.
Fluxo principal:
1. Sistema aplica prazo e horário definidos para a cor de risco.
2. Sistema consulta o mapeamento de CEP para bairro e UBS.
3. Paciente visualiza data, horário, prioridade e unidade indicada.
Fluxos alternativos e exceções:
A1 — CEP não mapeado: exibe orientação para confirmação da UBS com a Secretaria de Saúde.
UC06 — Visualizar comprovante de atendimento
Ator principal: Paciente
Objetivo: Obter o comprovante gerado após o agendamento.
Pré-condição: Etapas de cadastro, classificação e agendamento concluídas.
Pós-condição: Comprovante exibido e registro acrescentado ao histórico em texto.
Fluxo principal:
1. Sistema reúne nome, CPF, CNS, contato, risco, prioridade, data, horário, bairro, UBS e equipe responsável.
2. Sistema exibe o comprovante na tela.
3. Sistema acrescenta o comprovante ao arquivo comprovantes_sigasus.txt.
4. Paciente consulta as orientações de comparecimento.
Fluxos alternativos e exceções:
A1 — UBS não identificada: comprovante registra a orientação para confirmação da unidade.
A2 — Falha na gravação do arquivo: a persistência não pode ser considerada concluída; tratamento depende da implementação.
6. Registro de decisões e mudanças na modelagem
A modelagem foi corrigida para representar o paciente como único ator humano que opera o SIGASUS, eliminando os papéis indevidos de atendente e profissional de triagem. A classificação de risco foi tratada como regra e processamento interno do algoritmo, e não como tarefa humana. O agendamento automático, a escolha da UBS pelo CEP e a geração do comprovante foram relacionados ao objetivo do paciente de solicitar consulta. As validações e os prazos foram separados como regras de negócio para evitar confundir ações internas com casos de uso. A rastreabilidade foi refeita com base no detalhamento técnico do pré-projeto, distinguindo os objetivos do usuário das rotinas automatizadas.
7. Limites e consistência com o pré-projeto
A Parte B documenta a modelagem do SIGASUS para desenvolvimento em Java. O pré-projeto original foi utilizado como referência funcional e descreve a gravação de comprovantes em arquivo de texto; essa descrição não comprova a implementação em Java. Não há evidências de autenticação, banco de dados, disponibilidade de vagas em tempo real, integração oficial com o SUS ou confirmação da consulta por uma UBS. Por isso, esses recursos não foram apresentados como implementados. A modelagem considera os prazos por prioridade definidos no documento de requisitos, sem pressupor integração com agendas reais.
