
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Princípios Orçamentários Interativos</title>
    <!-- Chosen Palette: Calm Study -->
    <!-- Application Structure Plan: A aplicação é uma SPA com navegação lateral (esquerda) e uma área de conteúdo principal (direita). O usuário interage clicando em um princípio na navegação, que dinamicamente exibe os detalhes correspondentes na área principal. Esta estrutura foi escolhida para proporcionar um fluxo de exploração intuitivo e focado, permitindo que o usuário se aprofunde em um conceito por vez sem distrações. A navegação com ícones e texto claro otimiza a descoberta, enquanto o conteúdo bem segmentado facilita a compreensão. -->
    <!-- Visualization & Content Choices: Para apresentar os princípios orçamentários, que são conceitos textuais e não dados quantitativos, a abordagem de visualização e conteúdo foca em 'Organizar' e 'Informar'. Utilizamos cards de conteúdo estruturados com HTML e Tailwind CSS para cada princípio, que são dinamicamente exibidos/ocultados. Emojis Unicode são empregados como ícones interativos na navegação, reforçando a temática de cada princípio e tornando a interface mais engajadora. A interatividade principal reside na transição de conteúdo acionada pelo clique, garantindo uma experiência de aprendizado fluida. Além disso, foram adicionados botões para gerar cenários fictícios e uma caixa de perguntas interativa utilizando a API Gemini (gemini-2.0-flash), enriquecendo a contextualização e aprofundamento dos princípios. NÃO são utilizados SVG ou Mermaid JS, e bibliotecas de gráficos como Chart.js ou Plotly.js são desnecessárias para este tipo de conteúdo conceitual. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
        }
        .content-card {
            transition: opacity 0.5s ease-in-out, transform 0.3s ease-in-out;
            position: absolute; /* Para que apenas um card seja visível por vez, e a transição não cause reflow */
            width: 100%;
        }
        .content-card.hidden {
            opacity: 0;
            pointer-events: none;
            transform: translateY(10px);
        }
        .nav-item.active {
            background-color: #E0A75E; /* Cor de destaque */
            color: #3C3633;
            font-weight: 700;
        }
        .nav-item {
            transition: all 0.2s ease-in-out;
            cursor: pointer;
        }
        .nav-item:hover {
            transform: translateX(5px);
        }
        .emoji-button {
            transition: transform 0.2s ease-in-out;
            display: inline-block; /* Para permitir transform */
        }
        .nav-item:hover .emoji-button {
            transform: scale(1.2); /* Efeito de zoom no emoji ao passar o mouse */
        }
        .scenario-button {
            background-color: #7A9D54;
            color: white;
            padding: 0.75rem 1.5rem;
            border-radius: 0.5rem;
            font-weight: 500;
            transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out;
        }
        .scenario-button:hover {
            background-color: #5C7F3A;
            transform: translateY(-2px);
        }
        .scenario-button:active {
            transform: translateY(0);
        }
        .loading-spinner {
            border: 4px solid rgba(0, 0, 0, 0.1);
            border-left-color: #7A9D54;
            border-radius: 50%;
            width: 24px;
            height: 24px;
            animation: spin 1s linear infinite;
            display: inline-block;
            vertical-align: middle;
            margin-right: 8px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body class="bg-[#FDFBF6] text-[#3C3633]">

    <div class="container mx-auto p-4 md:p-8 max-w-6xl min-h-screen flex flex-col">

        <header class="text-center mb-8 md:mb-12">
            <h1 class="text-3xl md:text-4xl font-bold text-[#3C3633]">🎯 Princípios Orçamentários Clássicos (AFO)</h1>
            <p class="text-lg text-[#7A9D54] mt-2">Uma aplicação interativa para explorar os pilares da Administração Financeira e Orçamentária.</p>
            <p class="text-base text-gray-500 mt-4 max-w-2xl mx-auto">Navegue pelos princípios orçamentários essenciais clicando nas opções à esquerda. Cada seção oferece uma explicação detalhada, exemplos e legendas rápidas para facilitar o aprendizado. Experimente também a função ✨ **Gerar Cenário** para ver os princípios em ação, e agora o ✨ **Pergunte ao Gemini** para tirar suas dúvidas!</p>
        </header>

        <div class="flex flex-grow flex-col md:flex-row gap-8">

            <aside class="w-full md:w-1/4">
                <nav class="flex flex-row md:flex-col gap-2 bg-[#EEE7DA] p-3 rounded-lg shadow-sm">
                    <button data-target="unidade" class="nav-item flex items-center w-full text-left p-3 rounded-md hover:bg-[#E0A75E]/50">
                        <span class="emoji-button text-2xl mr-3">🏛️</span> Unidade
                    </button>
                    <button data-target="universalidade" class="nav-item flex items-center w-full text-left p-3 rounded-md hover:bg-[#E0A75E]/50">
                        <span class="emoji-button text-2xl mr-3">🌍</span> Universalidade
                    </button>
                    <button data-target="anualidade" class="nav-item flex items-center w-full text-left p-3 rounded-md hover:bg-[#E0A75E]/50">
                        <span class="emoji-button text-2xl mr-3">🗓️</span> Anualidade
                    </button>
                    <button data-target="legalidade" class="nav-item flex items-center w-full text-left p-3 rounded-md hover:bg-[#E0A75E]/50">
                        <span class="emoji-button text-2xl mr-3">⚖️</span> Legalidade
                    </button>
                    <button data-target="complemento" class="nav-item flex items-center w-full text-left p-3 rounded-md hover:bg-[#E0A75E]/50">
                        <span class="emoji-button text-2xl mr-3">✨</span> Complemento: EC 109/2021
                    </button>
                </nav>
            </aside>

            <main class="w-full md:w-3/4 relative min-h-[400px]">
                
                <div id="content-unidade" class="content-card space-y-4 top-0 left-0">
                    <div class="p-6 bg-white rounded-lg border border-gray-200 shadow-sm">
                        <h2 class="text-2xl font-bold mb-2 text-[#7A9D54] flex items-center"><span class="text-3xl mr-3">🏛️</span> Princípio da Unidade</h2>
                        <p class="text-gray-600">Esta seção detalha o Princípio da Unidade, que é fundamental para a organização fiscal. Ele estabelece que cada ente federativo deve possuir apenas um orçamento abrangente, garantindo clareza e controle sobre todas as receitas e despesas. Ao entender este princípio, você compreende a base da gestão orçamentária unificada.</p>
                    </div>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div class="p-4 bg-white rounded-lg border border-gray-200">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">💡 Conceito Chave</h3>
                            <p>Apenas **1 orçamento por ente federativo** (União, Estados, DF, Municípios).</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">📜 Base Legal</h3>
                            <p>Art. 2º da Lei nº 4.320, de 17 de março de 1964.</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">📝 Exemplo Prático</h3>
                            <p>A União tem uma única Lei Orçamentária Anual (LOA) que engloba os orçamentos Fiscal, da Seguridade Social e de Investimento das Estatais.</p>
                        </div>
                        <div class="p-4 bg-[#EEE7DA] rounded-lg border border-gray-200 col-span-1 md:col-span-2">
                            <h3 class="font-bold mb-1 text-[#3C3633]">🔑 Legenda Rápida</h3>
                            <p>Um só orçamento para cada nível de governo.</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2 flex flex-col items-start">
                            <button class="scenario-button" data-principle="Unidade" data-target="unidade">✨ Gerar Cenário</button>
                            <div id="scenario-unidade" class="scenario-output mt-4 text-gray-700 italic min-h-[40px]"></div>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2 flex flex-col items-start">
                            <h3 class="font-bold mb-2 text-[#7A9D54]">💬 Pergunte ao Gemini sobre este Princípio</h3>
                            <textarea id="question-unidade" class="w-full p-2 border border-gray-300 rounded-md mb-2 focus:ring-[#7A9D54] focus:border-[#7A9D54]" rows="2" placeholder="Ex: Qual a importância da Unidade para a fiscalização?"></textarea>
                            <button class="ask-button scenario-button" data-principle="Unidade" data-target="unidade">Perguntar</button>
                            <div id="answer-unidade" class="answer-output mt-4 text-gray-700 italic min-h-[40px]"></div>
                        </div>
                    </div>
                </div>
                
                <div id="content-universalidade" class="content-card hidden space-y-4 top-0 left-0">
                     <div class="p-6 bg-white rounded-lg border border-gray-200 shadow-sm">
                        <h2 class="text-2xl font-bold mb-2 text-[#7A9D54] flex items-center"><span class="text-3xl mr-3">🌍</span> Princípio da Universalidade</h2>
                        <p class="text-gray-600">Aqui exploramos o Princípio da Universalidade, que garante a transparência total do orçamento. Ele exige que absolutamente todas as receitas e despesas sejam incluídas na LOA, evitando que qualquer transação financeira do Estado ocorra sem a devida autorização legislativa. Compreender este princípio é crucial para assegurar a fiscalização completa das finanças públicas.</p>
                    </div>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div class="p-4 bg-white rounded-lg border border-gray-200">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">💡 Conceito Chave</h3>
                            <p>**Todas as receitas e despesas** devem ser contidas na LOA.</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">🔗 Relação</h3>
                            <p>Complementa diretamente o Princípio da Unidade, oferecendo uma visão ainda mais completa e detalhada.</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">🚫 Proíbe</h3>
                            <p>A ocorrência de "omissões" ou "orçamentos secretos", garantindo a publicidade e o controle total dos recursos.</p>
                        </div>
                         <div class="p-4 bg-[#EEE7DA] rounded-lg border border-gray-200 col-span-1 md:col-span-2">
                            <h3 class="font-bold mb-1 text-[#3C3633]">🔑 Legenda Rápida</h3>
                            <p>Nada pode ficar de fora do orçamento.</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2 flex flex-col items-start">
                            <button class="scenario-button" data-principle="Universalidade" data-target="universalidade">✨ Gerar Cenário</button>
                            <div id="scenario-universalidade" class="scenario-output mt-4 text-gray-700 italic min-h-[40px]"></div>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2 flex flex-col items-start">
                            <h3 class="font-bold mb-2 text-[#7A9D54]">💬 Pergunte ao Gemini sobre este Princípio</h3>
                            <textarea id="question-universalidade" class="w-full p-2 border border-gray-300 rounded-md mb-2 focus:ring-[#7A9D54] focus:border-[#7A9D54]" rows="2" placeholder="Ex: Qual o risco de não aplicar a Universalidade?"></textarea>
                            <button class="ask-button scenario-button" data-principle="Universalidade" data-target="universalidade">Perguntar</button>
                            <div id="answer-universalidade" class="answer-output mt-4 text-gray-700 italic min-h-[40px]"></div>
                        </div>
                    </div>
                </div>

                <div id="content-anualidade" class="content-card hidden space-y-4 top-0 left-0">
                    <div class="p-6 bg-white rounded-lg border border-gray-200 shadow-sm">
                        <h2 class="text-2xl font-bold mb-2 text-[#7A9D54] flex items-center"><span class="text-3xl mr-3">🗓️</span> Princípio da Anualidade</h2>
                        <p class="text-gray-600">O Princípio da Anualidade, ou Periodicidade, é vital para o planejamento e controle fiscal. Ele estabelece que o orçamento deve ter uma vigência limitada a um ano, coincidindo com o ano civil no Brasil (1º de janeiro a 31 de dezembro). Este ciclo anual permite a avaliação contínua do desempenho financeiro e a adaptação às novas realidades econômicas.</p>
                    </div>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div class="p-4 bg-white rounded-lg border border-gray-200">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">💡 Conceito Chave</h3>
                            <p>Vigência de **1 ano** (1º de janeiro a 31 de dezembro).</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">📜 Base Legal</h3>
                            <p>Art. 34 da Lei nº 4.320/64.</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2">
                            <h3 class="font-bold text-[#3C3633] mb-1 flex items-center"><span class="text-xl mr-2">⚠️</span> Atenção Importante</h3>
                            <p>Não confundir com o Plano Plurianual (PPA), que define diretrizes de médio prazo e tem vigência de 4 anos.</p>
                        </div>
                         <div class="p-4 bg-[#EEE7DA] rounded-lg border border-gray-200 col-span-1 md:col-span-2">
                            <h3 class="font-bold mb-1 text-[#3C3633]">🔑 Legenda Rápida</h3>
                            <p>Orçamento válido para um ano fiscal.</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2 flex flex-col items-start">
                            <button class="scenario-button" data-principle="Anualidade" data-target="anualidade">✨ Gerar Cenário</button>
                            <div id="scenario-anualidade" class="scenario-output mt-4 text-gray-700 italic min-h-[40px]"></div>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2 flex flex-col items-start">
                            <h3 class="font-bold mb-2 text-[#7A9D54]">💬 Pergunte ao Gemini sobre este Princípio</h3>
                            <textarea id="question-anualidade" class="w-full p-2 border border-gray-300 rounded-md mb-2 focus:ring-[#7A9D54] focus:border-[#7A9D54]" rows="2" placeholder="Ex: Qual a relação entre Anualidade e PPA?"></textarea>
                            <button class="ask-button scenario-button" data-principle="Anualidade" data-target="anualidade">Perguntar</button>
                            <div id="answer-anualidade" class="answer-output mt-4 text-gray-700 italic min-h-[40px]"></div>
                        </div>
                    </div>
                </div>

                <div id="content-legalidade" class="content-card hidden space-y-4 top-0 left-0">
                    <div class="p-6 bg-white rounded-lg border border-gray-200 shadow-sm">
                        <h2 class="text-2xl font-bold mb-2 text-[#7A9D54] flex items-center"><span class="text-3xl mr-3">⚖️</span> Princípio da Legalidade</h2>
                        <p class="text-gray-600">Nesta seção, abordamos o Princípio da Legalidade, que é um pilar da administração pública. Ele exige que a elaboração, aprovação e execução do orçamento sejam realizadas estritamente conforme o previsto em lei. Ou seja, nenhum gasto ou receita pode ser realizado sem prévia autorização legislativa, garantindo a legitimidade dos atos financeiros do Estado.</p>
                    </div>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div class="p-4 bg-white rounded-lg border border-gray-200">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">💡 Conceito Chave</h3>
                            <p>Todo ato orçamentário **precisa ser instituído por lei**.</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">📜 Base Legal</h3>
                            <p>Fundamentado na Constituição Federal (CF/88) e na Lei de Responsabilidade Fiscal (LRF).</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">✨ Reforço Recente</h3>
                            <p>A Emenda Constitucional nº 109/2021 (EC 109/2021) reforça este princípio ao prever a aplicação de gatilhos automáticos de ajuste fiscal, que são mecanismos legais.</p>
                        </div>
                         <div class="p-4 bg-[#EEE7DA] rounded-lg border border-gray-200 col-span-1 md:col-span-2">
                            <h3 class="font-bold mb-1 text-[#3C3633]">🔑 Legenda Rápida</h3>
                            <p>Orçamento deve ser legalmente aprovado.</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2 flex flex-col items-start">
                            <button class="scenario-button" data-principle="Legalidade" data-target="legalidade">✨ Gerar Cenário</button>
                            <div id="scenario-legalidade" class="scenario-output mt-4 text-gray-700 italic min-h-[40px]"></div>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2 flex flex-col items-start">
                            <h3 class="font-bold mb-2 text-[#7A9D54]">💬 Pergunte ao Gemini sobre este Princípio</h3>
                            <textarea id="question-legalidade" class="w-full p-2 border border-gray-300 rounded-md mb-2 focus:ring-[#7A9D54] focus:border-[#7A9D54]" rows="2" placeholder="Ex: A Legalidade impede mudanças no orçamento?"></textarea>
                            <button class="ask-button scenario-button" data-principle="Legalidade" data-target="legalidade">Perguntar</button>
                            <div id="answer-legalidade" class="answer-output mt-4 text-gray-700 italic min-h-[40px]"></div>
                        </div>
                    </div>
                </div>

                <div id="content-complemento" class="content-card hidden space-y-4 top-0 left-0">
                    <div class="p-6 bg-white rounded-lg border border-gray-200 shadow-sm">
                        <h2 class="text-2xl font-bold mb-2 text-[#7A9D54] flex items-center"><span class="text-3xl mr-3">✨</span> Complemento: Emenda Constitucional 109/2021</h2>
                        <p class="text-gray-600">Esta seção aborda as importantes inovações trazidas pela EC 109/2021 para o controle fiscal brasileiro. Ela introduz mecanismos automáticos de ajuste que são acionados em situações de desequilíbrio nas contas públicas. Entender esta emenda é crucial para compreender as restrições fiscais e os gatilhos que podem ser aplicados na gestão orçamentária.</p>
                    </div>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div class="p-4 bg-white rounded-lg border border-gray-200">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">💡 Conceito Central</h3>
                            <p>Introduz **Gatilhos de ajuste** para o orçamento, mecanismos automáticos de controle.</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">🗺️ Abrangência</h3>
                            <p>Sua aplicação se estende à União, Estados, Municípios e Distrito Federal.</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2">
                            <h3 class="font-bold mb-1 text-[#E0A75E]">🔒 Efeito Prático</h3>
                            <p>Impõe **restrições automáticas** (ex: vedações a aumentos, contratações) quando certas condições fiscais são atingidas.</p>
                        </div>
                         <div class="p-4 bg-[#EEE7DA] rounded-lg border border-gray-200 col-span-1 md:col-span-2">
                            <h3 class="font-bold mb-1 text-[#3C3633]">🔑 Legenda Rápida</h3>
                            <p>Emenda que adiciona regras e limites automáticos.</p>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2 flex flex-col items-start">
                            <button class="scenario-button" data-principle="Complemento: EC 109/2021" data-target="complemento">✨ Gerar Cenário</button>
                            <div id="scenario-complemento" class="scenario-output mt-4 text-gray-700 italic min-h-[40px]"></div>
                        </div>
                        <div class="p-4 bg-white rounded-lg border border-gray-200 col-span-1 md:col-span-2 flex flex-col items-start">
                            <h3 class="font-bold mb-2 text-[#7A9D54]">💬 Pergunte ao Gemini sobre este Princípio</h3>
                            <textarea id="question-complemento" class="w-full p-2 border border-gray-300 rounded-md mb-2 focus:ring-[#7A9D54] focus:border-[#7A9D54]" rows="2" placeholder="Ex: Quais são os principais gatilhos da EC 109/2021?"></textarea>
                            <button class="ask-button scenario-button" data-principle="Complemento: EC 109/2021" data-target="complemento">Perguntar</button>
                            <div id="answer-complemento" class="answer-output mt-4 text-gray-700 italic min-h-[40px]"></div>
                        </div>
                    </div>
                </div>
            </main>
        </div>
    </div>

    <script>
        const navButtons = document.querySelectorAll('.nav-item');
        const contentCards = document.querySelectorAll('.content-card');
        const scenarioButtons = document.querySelectorAll('.scenario-button');
        const scenarioOutputs = document.querySelectorAll('.scenario-output');
        const askButtons = document.querySelectorAll('.ask-button'); // Novo seletor para os botões de pergunta
        const answerOutputs = document.querySelectorAll('.answer-output'); // Novo seletor para as saídas de resposta

        // Função para gerar cenário usando a API Gemini
        async function generateScenario(principleName, outputElement) {
            outputElement.innerHTML = '<span class="loading-spinner"></span> Gerando cenário...';
            outputElement.style.color = '#7A9D54';
            outputElement.style.fontWeight = '500';

            const prompt = `Gere um cenário fictício curto (2-3 frases) onde o Princípio Orçamentário da ${principleName} é claramente aplicado ou relevante. O cenário deve ser simples e didático.`;

            let chatHistory = [];
            chatHistory.push({ role: "user", parts: [{ text: prompt }] });
            const payload = { contents: chatHistory };
            const apiKey = ""; // A API Key será automaticamente fornecida em tempo de execução pelo Canvas
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                const result = await response.json();
                if (result.candidates && result.candidates.length > 0 &&
                    result.candidates[0].content && result.candidates[0].content.parts &&
                    result.candidates[0].content.parts.length > 0) {
                    const text = result.candidates[0].content.parts[0].text;
                    outputElement.innerHTML = text;
                    outputElement.style.color = '#3C3633';
                    outputElement.style.fontWeight = 'normal';
                } else {
                    outputElement.innerHTML = 'Não foi possível gerar um cenário. Tente novamente.';
                    outputElement.style.color = 'red';
                    outputElement.style.fontWeight = 'normal';
                }
            } catch (error) {
                outputElement.innerHTML = 'Erro ao conectar com a API Gemini. Verifique sua conexão e tente novamente.';
                outputElement.style.color = 'red';
                outputElement.style.fontWeight = 'normal';
                console.error('Erro na chamada da API Gemini (cenário):', error);
            }
        }

        // Função para perguntar ao Gemini
        async function askGemini(principleName, question, outputElement) {
            if (!question.trim()) {
                outputElement.innerHTML = 'Por favor, digite sua pergunta.';
                outputElement.style.color = 'red';
                outputElement.style.fontWeight = 'normal';
                return;
            }

            outputElement.innerHTML = '<span class="loading-spinner"></span> Pensando na resposta...';
            outputElement.style.color = '#7A9D54';
            outputElement.style.fontWeight = '500';

            const prompt = `Considerando o Princípio Orçamentário da ${principleName}, responda à seguinte pergunta de forma concisa e didática: "${question}"`;

            let chatHistory = [];
            chatHistory.push({ role: "user", parts: [{ text: prompt }] });
            const payload = { contents: chatHistory };
            const apiKey = ""; // A API Key será automaticamente fornecida em tempo de execução pelo Canvas
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                const result = await response.json();
                if (result.candidates && result.candidates.length > 0 &&
                    result.candidates[0].content && result.candidates[0].content.parts &&
                    result.candidates[0].content.parts.length > 0) {
                    const text = result.candidates[0].content.parts[0].text;
                    outputElement.innerHTML = text;
                    outputElement.style.color = '#3C3633';
                    outputElement.style.fontWeight = 'normal';
                } else {
                    outputElement.innerHTML = 'Não foi possível obter uma resposta. Tente novamente.';
                    outputElement.style.color = 'red';
                    outputElement.style.fontWeight = 'normal';
                }
            } catch (error) {
                outputElement.innerHTML = 'Erro ao conectar com a API Gemini. Verifique sua conexão e tente novamente.';
                outputElement.style.color = 'red';
                outputElement.style.fontWeight = 'normal';
                console.error('Erro na chamada da API Gemini (pergunta):', error);
            }
        }

        function switchContent(targetId) {
            contentCards.forEach(card => {
                if (card.id === `content-${targetId}`) {
                    card.classList.remove('hidden');
                    card.classList.add('flex');
                    card.style.position = 'relative';
                } else {
                    card.classList.add('hidden');
                    card.classList.remove('flex');
                    card.style.position = 'absolute';
                    // Limpar qualquer cenário gerado e mensagem de erro ao trocar de card
                    const scenarioOutput = card.querySelector('.scenario-output');
                    if (scenarioOutput) {
                        scenarioOutput.innerHTML = '';
                    }
                    // Limpar qualquer resposta gerada pela pergunta ao Gemini
                    const answerOutput = card.querySelector('.answer-output');
                    if (answerOutput) {
                        answerOutput.innerHTML = '';
                    }
                }
            });

            navButtons.forEach(button => {
                if (button.dataset.target === targetId) {
                    button.classList.add('active');
                } else {
                    button.classList.remove('active');
                }
            });
        }

        navButtons.forEach(button => {
            button.addEventListener('click', () => {
                const target = button.dataset.target;
                switchContent(target);
            });
        });

        scenarioButtons.forEach(button => {
            button.addEventListener('click', () => {
                const principleName = button.dataset.principle;
                const outputElementId = `scenario-${button.dataset.target}`; // Usar data-target para o ID de saída
                const outputElement = document.getElementById(outputElementId);
                if (outputElement) {
                    generateScenario(principleName, outputElement);
                }
            });
        });

        askButtons.forEach(button => {
            button.addEventListener('click', () => {
                const principleName = button.dataset.principle;
                const questionInputId = `question-${button.dataset.target}`; // ID do textarea da pergunta
                const questionInput = document.getElementById(questionInputId);
                const answerOutputId = `answer-${button.dataset.target}`; // ID do div de resposta
                const answerOutput = document.getElementById(answerOutputId);

                if (questionInput && answerOutput) {
                    askGemini(principleName, questionInput.value, answerOutput);
                    questionInput.value = ''; // Limpa o campo de pergunta após o envio
                }
            });
        });

        document.addEventListener('DOMContentLoaded', () => {
            // Garante que o ID do data-target corresponda ao ID do div do conteúdo para os botões de cenário e pergunta
            scenarioButtons.forEach(button => {
                const principleName = button.dataset.principle;
                if (principleName === 'Complemento: EC 109/2021') {
                    button.dataset.target = 'complemento';
                } else {
                    button.dataset.target = principleName.toLowerCase();
                }
            });
            askButtons.forEach(button => {
                const principleName = button.dataset.principle;
                if (principleName === 'Complemento: EC 109/2021') {
                    button.dataset.target = 'complemento';
                } else {
                    button.dataset.target = principleName.toLowerCase();
                }
            });
            switchContent('unidade'); // Exibe o primeiro princípio por padrão ao carregar a página
        });
    </script>
</body>
</html>
