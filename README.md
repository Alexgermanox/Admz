<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bater Ponto</title>
    <!-- Inclui o Bootstrap para um design bonito -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.7/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-LN+7fdVzj6u52u30Kp6M/trliBMCMKTyK833zpbD+pXdCLuTusPj697FH4R/5mcr" crossorigin="anonymous">
    <!-- Inclui jsPDF e jsPDF-AutoTable para exportar PDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.8.3/jspdf.plugin.autotable.min.js"></script>
    <style>
        body {
            background-color: #f8f9fa; /* Fundo cinza claro */
            padding: 20px; /* Espaço nas bordas */
        }
        .escondido { display: none; } /* Classe para esconder elementos */
        .cartao { max-width: 600px; margin: auto; } /* Centraliza e limita largura */
        .tabela-responsiva { max-height: 400px; overflow-y: auto; overflow-x: auto; } /* Rolagem em tabelas grandes */
        #containerNotificacao {
            position: fixed;
            top: 20px;
            right: 20px;
            z-index: 1050; /* Notificações ficam na frente */
        }
        .foto-3x4 {
            width: 113px; /* 3 cm em 96 DPI */
            height: 151px; /* 4 cm em 96 DPI */
            object-fit: cover;
            border: 1px solid #ccc;
            margin-bottom: 15px;
            display: block;
            margin-left: auto;
            margin-right: auto;
        }
        .form-label-ajustado {
            margin-left: 0; /* Reduz o recuo padrão do Bootstrap */
        }
        .linha-separadora {
            height: 20px; /* Linha vazia entre tabelas */
            background-color: transparent;
        }
        /* Estilo moderno para botões na tela de Bater Ponto */
        .botao-ajustado {
            font-size: 0.9rem; /* Tamanho da fonte */
            font-weight: bold; /* Texto em negrito */
            padding: 0.75rem 1rem; /* Padding ajustado */
            border-radius: 10px; /* Bordas arredondadas */
            border: none; /* Remove borda padrão */
            background: linear-gradient(145deg, rgba(255,255,255,0.2), rgba(0,0,0,0.2)); /* Gradiente sutil */
            box-shadow: 3px 3px 8px rgba(0,0,0,0.2), -3px -3px 8px rgba(255,255,255,0.2); /* Efeito 3D */
            transition: transform 0.2s, box-shadow 0.2s; /* Transições suaves */
            white-space: nowrap; /* Evita quebra de texto */
            text-overflow: ellipsis; /* Reticências se texto for longo */
            overflow: hidden; /* Evita transbordo */
        }
        /* Hover para interatividade */
        .botao-ajustado:hover {
            transform: translateY(-2px); /* Levanta o botão */
            box-shadow: 5px 5px 12px rgba(0,0,0,0.3), -5px -5px 12px rgba(255,255,255,0.3); /* Sombra mais intensa */
        }
        /* Cores específicas para cada botão com gradiente */
        .btn-success.botao-ajustado {
            background: linear-gradient(145deg, #28a745, #1e7e34); /* Verde moderno */
        }
        .btn-warning.botao-ajustado {
            background: linear-gradient(145deg, #ffc107, #e0a800); /* Amarelo vibrante */
        }
        .btn-info.botao-ajustado {
            background: linear-gradient(145deg, #17a2b8, #117a8b); /* Azul moderno */
        }
        .btn-danger.botao-ajustado {
            background: linear-gradient(145deg, #dc3545, #bd2130); /* Vermelho intenso */
        }
        /* Responsividade para telas menores */
        @media (max-width: 576px) {
            .botao-ajustado {
                font-size: 0.8rem; /* Fonte menor em celulares */
                padding: 0.6rem 0.8rem; /* Padding reduzido */
            }
            .row-botao-ponto {
                gap: 0.5rem; /* Espaço entre botões empilhados */
            }
        }
        /* Estilo para nome e CPF acima da tabela */
        .tabela-cabecalho {
            text-align: center;
            margin-bottom: 1rem;
        }
        .tabela-cabecalho h3 {
            margin-bottom: 0.2rem;
            font-size: 1.5rem;
            font-weight: bold;
        }
        .tabela-cabecalho p {
            margin-bottom: 0;
            font-size: 1rem;
            color: #555;
        }
        /* Estilo para a linha de assinatura */
        .assinatura {
            margin-top: 1rem;
            text-align: center;
        }
        .assinatura hr {
            border-top: 2px solid #000;
            width: 200px;
            margin: 0 auto 0.5rem auto;
        }
        .assinatura p {
            font-size: 1rem;
            font-style: italic;
            color: #333;
        }
        /* Garantir bordas visíveis na tabela */
        .table-bordered th,
        .table-bordered td {
            border: 1px solid #dee2e6 !important; /* Bordas visíveis */
        }
        /* Estilo para o botão de exportar PDF */
        #botaoExportarPDF {
            margin-bottom: 1rem;
        }
        /* Ocultar a linha de assinatura apenas na interface.
   (Os PDFs continuam gerando a assinatura via jsPDF.) */
.assinatura { display: none !important; }
    </style>
</head>
<body>
    <!-- Área para notificações (toasts) -->
    <div id="containerNotificacao"></div>

    <!-- Tela de Login -->
    <div id="telaLogin" class="container mt-5">
        <div class="cartao shadow">
            <div class="card-body">
                <h1 class="card-title text-center">Entrar</h1>
                <input type="text" id="nomeUsuarioLogin" class="form-control mb-3" placeholder="Nome de usuário" required>
                <input type="password" id="senhaLogin" class="form-control mb-3" placeholder="Senha" required>
                <button id="botaoLogin" class="btn btn-primary w-100 mb-2" onclick="entrar()">Entrar</button>
            </div>
        </div>
    </div>

    <!-- Tela Inicial do Admin -->
    <div id="telaInicialAdmin" class="container mt-5 escondido">
        <div class="cartao shadow">
            <div class="card-body">
                <h1 class="card-title text-center">Painel do Administrador</h1>
                <p class="text-center">Bem-vindo, <span id="usuarioAdmin"></span></p>
                <button class="btn btn-primary w-100 mb-2" onclick="mostrarTelaPrincipal()">Bater Ponto</button>
                <button class="btn btn-primary w-100 mb-2" onclick="mostrarTelaGerenciarUsuarios()">Gerenciar Usuários</button>
                <button class="btn btn-primary w-100 mb-2" onclick="mostrarTelaPassagens()">Passagens</button>
                <button class="btn btn-outline-primary w-100" onclick="sair()">Sair</button>
            </div>
        </div>
    </div>

    <!-- Tela de Cadastro -->
    <div id="telaCadastro" class="container mt-5 escondido">
        <div class="cartao shadow">
            <div class="card-body">
                <h1 class="card-title text-center">Cadastrar Usuário</h1>
                <div class="mb-3">
                    <label for="nomeUsuarioCadastro" class="form-label form-label-ajustado">Nome de Usuário</label>
                    <input type="text" id="nomeUsuarioCadastro" class="form-control" required>
                </div>
                <div class="mb-3">
                    <label for="nomeCompletoCadastro" class="form-label form-label-ajustado">Nome Completo</label>
                    <input type="text" id="nomeCompletoCadastro" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="cpfCadastro" class="form-label form-label-ajustado">CPF</label>
                    <input type="text" id="cpfCadastro" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="dataNascimentoCadastro" class="form-label form-label-ajustado">Data de Nascimento</label>
                    <input type="date" id="dataNascimentoCadastro" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="enderecoCadastro" class="form-label form-label-ajustado">Endereço</label>
                    <input type="text" id="enderecoCadastro" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="dataAdmissaoCadastro" class="form-label form-label-ajustado">Data de Admissão</label>
                    <input type="date" id="dataAdmissaoCadastro" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="cargoCadastro" class="form-label form-label-ajustado">Cargo</label>
                    <input type="text" id="cargoCadastro" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="pisPasepCadastro" class="form-label form-label-ajustado">PIS/PASEP</label>
                    <input type="text" id="pisPasepCadastro" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="ctpsCadastro" class="form-label form-label-ajustado">CTPS</label>
                    <input type="text" id="ctpsCadastro" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="senhaCadastro" class="form-label form-label-ajustado">Senha</label>
                    <input type="password" id="senhaCadastro" class="form-control" required>
                </div>
                <div class="mb-3 form-check">
                    <input type="checkbox" id="ehAdmin" class="form-check-input">
                    <label for="ehAdmin" class="form-label form-label-ajustado">Administrador</label>
                </div>
                <div class="mb-3">
                    <input type="file" id="uploadFotoCadastro" class="form-control" accept="image/*">
                </div>
                <button id="botaoCadastrar" class="btn btn-primary w-100 mb-2" onclick="cadastrar()">Cadastrar</button>
                <button id="botaoVoltarGerenciar" class="btn btn-secondary w-100" onclick="mostrarTelaGerenciarUsuarios()">Voltar</button>
            </div>
        </div>
    </div>

    <!-- Tela de Gerenciamento de Usuários -->
    <div id="telaGerenciarUsuarios" class="container mt-5 escondido">
        <div class="cartao shadow">
            <div class="card-body">
                <h1 class="card-title text-center">Gerenciar Usuários</h1>
                <button class="btn btn-primary w-100 mb-3" onclick="mostrarTelaCadastro()">Cadastrar Novo Usuário</button>
                <h3>Usuários</h3>
                <div class="mb-4">
                    <select id="selecionarUsuario" class="form-select" onchange="mostrarTelaPerfilUsuario()">
                        <option value="">Selecione um usuário</option>
                    </select>
                </div>
                <button class="btn btn-secondary w-100 mt-3" onclick="mostrarTelaInicialAdmin()">Voltar</button>
            </div>
        </div>
    </div>

    <!-- Tela de Perfil do Usuário -->
    <div id="telaPerfilUsuario" class="container mt-5 escondido">
        <div class="cartao shadow">
            <div class="card-body">
                <h1 class="card-title text-center">Perfil do Usuário</h1>
                <div class="mb-3">
                    <img id="fotoPerfil" class="foto-3x4 escondido" alt="Foto 3x4">
                    <input type="file" id="uploadFoto" class="form-control" accept="image/*">
                    <button id="botaoRemoverFoto" class="btn btn-outline-danger w-100 mt-2" onclick="removerFoto()">Remover Foto</button>
                </div>
                <div class="mb-3">
                    <label for="editarNomeUsuario" class="form-label form-label-ajustado">Nome de Usuário</label>
                    <input type="text" id="editarNomeUsuario" class="form-control" required>
                </div>
                <div class="mb-3">
                    <label for="editarNomeCompleto" class="form-label form-label-ajustado">Nome Completo</label>
                    <input type="text" id="editarNomeCompleto" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="editarCpf" class="form-label form-label-ajustado">CPF</label>
                    <input type="text" id="editarCpf" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="editarDataNascimento" class="form-label form-label-ajustado">Data de Nascimento</label>
                    <input type="date" id="editarDataNascimento" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="editarEndereco" class="form-label form-label-ajustado">Endereço</label>
                    <input type="text" id="editarEndereco" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="editarDataAdmissao" class="form-label form-label-ajustado">Data de Admissão</label>
                    <input type="date" id="editarDataAdmissao" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="editarCargo" class="form-label form-label-ajustado">Cargo</label>
                    <input type="text" id="editarCargo" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="editarPisPasep" class="form-label form-label-ajustado">PIS/PASEP</label>
                    <input type="text" id="editarPisPasep" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="editarCtps" class="form-label form-label-ajustado">CTPS</label>
                    <input type="text" id="editarCtps" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="editarSenhaUsuario" class="form-label form-label-ajustado">Senha</label>
                    <input type="password" id="editarSenhaUsuario" class="form-control" required>
                </div>
                <div class="mb-3 form-check">
                    <input type="checkbox" id="editarEhAdmin" class="form-check-input">
                    <label for="editarEhAdmin" class="form-label form-label-ajustado">Administrador</label>
                </div>
                <input type="hidden" id="nomeUsuarioOriginal">
                <button class="btn btn-info w-100 mb-2" onclick="exportarPerfilPDF()">Exportar PDF</button>
                <button class="btn btn-primary w-100 mb-2" onclick="salvarEdicaoUsuario()">Salvar</button>
                <button class="btn btn-danger w-100 mb-2" data-bs-toggle="modal" data-bs-target="#modalExcluirUsuario">Excluir Usuário</button>
                <button class="btn btn-secondary w-100" onclick="mostrarTelaGerenciarUsuarios()">Voltar</button>
            </div>
        </div>
        <div id="registrosUsuario" class="mt-4">
            <h2 class="text-center">Registros do Usuário</h2>
            <div class="text-center mb-3">
                <button id="botaoExportarPDF" class="btn btn-info botao-ajustado escondido" onclick="exportarRegistrosPDF()">Exportar PDF</button>
            </div>
            <div class="tabela-responsiva">
                <div class="tabela-cabecalho">
                    <h3 id="nomeCompletoUsuarioPerfil"></h3>
                    <p id="cpfUsuarioPerfil"></p>
                </div>
                <table class="table table-striped table-hover table-bordered">
                    <thead class="table-dark">
                        <tr>
                            <th>Dia</th>
                            <th>Entrada</th>
                            <th>Pausa</th>
                            <th>Retorno</th>
                            <th>Saída</th>
                        </tr>
                    </thead>
                    <tbody id="corpoRegistrosUsuario"></tbody>
                </table>
                <div id="relatorioUsuario" class="mt-3">
                    <h3 class="text-center">Relatório</h3>
                    <pre id="conteudoRelatorioUsuario" class="mb-0"></pre>
                </div>
                <div class="assinatura">
                    <hr>
                    <p>Assinatura do funcionário</p>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal para Editar Registro -->
    <div class="modal fade" id="modalEditarRegistro" tabindex="-1" aria-labelledby="rotuloModalEditarRegistro" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="rotuloModalEditarRegistro">Editar Registro</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <div class="mb-3">
                        <label for="editarDataHoraRegistro" class="form-label">Data/Hora</label>
                        <input type="datetime-local" id="editarDataHoraRegistro" class="form-control" required>
                    </div>
                    <div class="mb-3">
                        <label for="editarTipoRegistro" class="form-label">Tipo</label>
                        <select id="editarTipoRegistro" class="form-select" required>
                            <option value="Entrada">Entrada</option>
                            <option value="Pausa">Pausa</option>
                            <option value="Retorno">Retorno</option>
                            <option value="Saída">Saída</option>
                        </select>
                    </div>
                    <input type="hidden" id="indiceRegistro">
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
                    <button type="button" class="btn btn-primary" onclick="salvarEdicaoRegistro()">Salvar</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal para Confirmar Exclusão de Registro -->
    <div class="modal fade" id="modalExcluirRegistro" tabindex="-1" aria-labelledby="rotuloModalExcluirRegistro" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="rotuloModalExcluirRegistro">Confirmação</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    Tem certeza que deseja excluir este registro? Esta ação não pode ser desfeita.
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
                    <button type="button" class="btn btn-danger" onclick="excluirRegistro()">Excluir</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal para Confirmar Exclusão de Usuário -->
    <div class="modal fade" id="modalExcluirUsuario" tabindex="-1" aria-labelledby="rotuloModalExcluirUsuario" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="rotuloModalExcluirUsuario">Confirmação</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    Tem certeza que deseja excluir este usuário? Esta ação não pode ser desfeita.
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
                    <button type="button" class="btn btn-danger" onclick="excluirUsuario()">Excluir</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Tela de Passagens (Placeholder) -->
    <div id="telaPassagens" class="container mt-5 escondido">
        <div class="cartao shadow">
            <div class="card-body">
                <h1 class="card-title text-center">Passagens</h1>
                <p class="text-center">Funcionalidade em desenvolvimento.</p>
                <button class="btn btn-secondary w-100" onclick="mostrarTelaInicialAdmin()">Voltar</button>
            </div>
        </div>
    </div>

    <!-- Tela Principal (Bater Ponto) -->
    <div id="telaPrincipal" class="container mt-5 escondido">
        <div class="cartao shadow">
            <div class="card-body">
                <h1 class="card-title text-center">Bater Ponto</h1>
                <p class="text-center">Usuário: <span id="usuarioAtual"></span></p>
                <p class="text-center">Horário atual: <span id="horarioAtual"></span></p>
                <div class="row justify-content-center mb-3 row-botao-ponto g-2">
                    <div class="col-3 col-sm-12">
                        <button id="botaoEntrada" class="btn btn-success w-100 botao-ajustado" onclick="registrarPonto('Entrada')">Entrada</button>
                    </div>
                    <div class="col-3 col-sm-12">
                        <button id="botaoPausa" class="btn btn-warning w-100 botao-ajustado" onclick="registrarPonto('Pausa')">Pausa</button>
                    </div>
                    <div class="col-3 col-sm-12">
                        <button id="botaoRetorno" class="btn btn-info w-100 botao-ajustado" onclick="registrarPonto('Retorno')">Retorno</button>
                    </div>
                    <div class="col-3 col-sm-12">
                        <button id="botaoSaida" class="btn btn-danger w-100 botao-ajustado" onclick="registrarPonto('Saída')">Saída</button>
                    </div>
                </div>
                <div class="row justify-content-center">
                    <div class="col-md-3">
                        <button id="botaoVoltarAdmin" class="btn btn-secondary w-100 mb-2 escondido" onclick="mostrarTelaInicialAdmin()">Voltar ao Painel</button>
                    </div>
                    <div class="col-md-3">
                        <button id="botaoExportar" class="btn btn-info w-100 mb-2" onclick="exportarParaCSV()">Exportar CSV</button>
                    </div>
                    <div class="col-md-3">
                        <button id="botaoLimpar" class="btn btn-secondary w-100 mb-2" data-bs-toggle="modal" data-bs-target="#modalLimpar">Limpar Registros</button>
                    </div>
                    <div class="col-md-3">
                        <button id="botaoSair" class="btn btn-outline-primary w-100 mb-2" onclick="sair()">Sair</button>
                    </div>
                </div>
            </div>
        </div>
        <div id="registros" class="mt-4">
            <h2 class="text-center">Meus Registros</h2>
            <div class="tabela-responsiva">
                <div class="tabela-cabecalho">
                    <h3 id="nomeCompletoUsuario"></h3>
                    <p id="cpfUsuario"></p>
                </div>
                <table class="table table-striped table-hover table-bordered">
                    <thead class="table-dark">
                        <tr>
                            <th>Dia</th>
                            <th>Entrada</th>
                            <th>Pausa</th>
                            <th>Retorno</th>
                            <th>Saída</th>
                        </tr>
                    </thead>
                    <tbody id="tabelasRegistros"></tbody>
                </table>
                <div id="relatorio" class="mt-3">
                    <h3 class="text-center">Relatório</h3>
                    <pre id="conteudoRelatorio" class="mb-0"></pre>
                </div>
                <div class="assinatura">
                    <hr>
                    <p>Assinatura do funcionário</p>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal para Confirmar Limpeza de Registros -->
    <div class="modal fade" id="modalLimpar" tabindex="-1" aria-labelledby="rotuloModalLimpar" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="rotuloModalLimpar">Confirmação</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    Tem certeza que deseja limpar todos os registros? Esta ação não pode be undone.
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
                    <button type="button" class="btn btn-danger" onclick="limparRegistros()">Limpar</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Criar administrador padrão na primeira vez
        (function inicializarAdmin() {
            const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
            if (!usuarios.some(usuario => usuario.nome === 'admin')) {
                usuarios.push({
                    nome: 'admin',
                    senha: 'admin123',
                    ehAdmin: true,
                    nomeCompleto: 'Administrador do Sistema',
                    cpf: '',
                    dataNascimento: '',
                    endereco: '',
                    dataAdmissao: '',
                    cargo: 'Administrador',
                    pisPasep: '',
                    ctps: '',
                    foto: ''
                });
                localStorage.setItem('usuarios', JSON.stringify(usuarios));
            }
        })();

        // Atualizar horário atual
        function atualizarHorario() {
            const agora = new Date();
            document.getElementById('horarioAtual').textContent = agora.toLocaleString('pt-BR');
        }
        setInterval(atualizarHorario, 1000);
        atualizarHorario();

        // Mostrar notificação (toast)
        function mostrarNotificacao(mensagem, tipo = 'success') {
            const container = document.getElementById('containerNotificacao');
            const notificacao = document.createElement('div');
            notificacao.className = `toast align-items-center text-white bg-${tipo} border-0`;
            notificacao.setAttribute('role', 'alert');
            notificacao.setAttribute('aria-live', 'assertive');
            notificacao.setAttribute('aria-atomic', 'true');
            notificacao.innerHTML = `
                <div class="d-flex">
                    <div class="toast-body">${mensagem}</div>
                    <button type="button" class="btn-close btn-close-white me-2 m-auto" data-bs-dismiss="toast" aria-label="Close"></button>
                </div>
            `;
            container.appendChild(notificacao);
            new bootstrap.Toast(notificacao).show();
            setTimeout(() => notificacao.remove(), 3000);
        }

        // Aplicar máscara para CPF
        function aplicarMascaraCPF(cpf) {
            cpf = cpf.replace(/\D/g, '');
            if (cpf.length > 11) cpf = cpf.slice(0, 11);
            return cpf.replace(/(\d{3})(\d{3})(\d{3})(\d{2})/, '$1.$2.$3-$4');
        }

        document.getElementById('cpfCadastro').addEventListener('input', function(e) {
            e.target.value = aplicarMascaraCPF(e.target.value);
        });

        document.getElementById('editarCpf').addEventListener('input', function(e) {
            e.target.value = aplicarMascaraCPF(e.target.value);
        });

        // Aplicar máscara para PIS/PASEP
        function aplicarMascaraPisPasep(pis) {
            pis = pis.replace(/\D/g, '');
            if (pis.length > 11) pis = pis.slice(0, 11);
            return pis.replace(/(\d{3})(\d{5})(\d{2})(\d{1})/, '$1.$2.$3-$4');
        }

        document.getElementById('pisPasepCadastro').addEventListener('input', function(e) {
            e.target.value = aplicarMascaraPisPasep(e.target.value);
        });

        document.getElementById('editarPisPasep').addEventListener('input', function(e) {
            e.target.value = aplicarMascaraPisPasep(e.target.value);
        });

        // Validar CPF
        function validarCPF(cpf) {
            cpf = cpf.replace(/\D/g, '');
            return cpf.length === 11;
        }

        // Validar PIS/PASEP
        function validarPisPasep(pis) {
            pis = pis.replace(/\D/g, '');
            return pis.length === 11;
        }

        // Carregar foto como base64
        function carregarFoto(input, imgElementId) {
            const file = input.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    document.getElementById(imgElementId).src = e.target.result;
                    document.getElementById(imgElementId).classList.remove('escondido');
                };
                reader.readAsDataURL(file);
            }
        }

        document.getElementById('uploadFoto').addEventListener('change', function() {
            carregarFoto(this, 'fotoPerfil');
        });

        document.getElementById('uploadFotoCadastro').addEventListener('change', function() {
            carregarFoto(this, 'fotoPerfil');
        });

        // Remover foto
        function removerFoto() {
            const fotoPerfil = document.getElementById('fotoPerfil');
            const uploadFoto = document.getElementById('uploadFoto');
            fotoPerfil.src = '';
            fotoPerfil.classList.add('escondido');
            uploadFoto.value = '';
            mostrarNotificacao('Foto removida com sucesso!', 'success');
        }

        // Exportar perfil para PDF
        function exportarPerfilPDF() {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();
            const usuario = JSON.parse(localStorage.getItem('usuarios') || '[]').find(u => u.nome === document.getElementById('nomeUsuarioOriginal').value);
            if (!usuario) {
                mostrarNotificacao('Usuário não encontrado.', 'danger');
                return;
            }

            // Título
            doc.setFontSize(16);
            doc.text('Perfil do Usuário', 10, 10);

            // Dados do usuário
            let y = 20;
            doc.setFontSize(12);
            doc.text(`Nome de Usuário: ${usuario.nome || '-'}`, 10, y); y += 10;
            doc.text(`Nome Completo: ${usuario.nomeCompleto || '-'}`, 10, y); y += 10;
            doc.text(`CPF: ${usuario.cpf || '-'}`, 10, y); y += 10;
            doc.text(`Data de Nascimento: ${usuario.dataNascimento ? new Date(usuario.dataNascimento).toLocaleDateString('pt-BR') : '-'}`, 10, y); y += 10;
            doc.text(`Endereço: ${usuario.endereco || '-'}`, 10, y); y += 10;
            doc.text(`Data de Admissão: ${usuario.dataAdmissao ? new Date(usuario.dataAdmissao).toLocaleDateString('pt-BR') : '-'}`, 10, y); y += 10;
            doc.text(`Cargo: ${usuario.cargo || '-'}`, 10, y); y += 10;
            doc.text(`PIS/PASEP: ${usuario.pisPasep || '-'}`, 10, y); y += 10;
            doc.text(`CTPS: ${usuario.ctps || '-'}`, 10, y); y += 10;
            doc.text(`Administrador: ${usuario.ehAdmin ? 'Sim' : 'Não'}`, 10, y); y += 10;

            // Adicionar foto, se disponível
            if (usuario.foto && !usuario.foto.includes('data:,')) {
                try {
                    doc.addImage(usuario.foto, 'JPEG', 150, 20, 30, 40);
                } catch (e) {
                    console.error('Erro ao adicionar imagem ao PDF:', e);
                }
            }

            doc.save(`perfil_${usuario.nome}.pdf`);
            mostrarNotificacao('Perfil exportado como PDF com sucesso!', 'success');
        }
// ====== Utilitários: Fim de semana ======
function ehFimDeSemana(data) {
    const d = data.getDay(); // 0 = Domingo, 6 = Sábado
    return d === 0 || d === 6;
}
function rotuloFimDeSemana(data) {
    return data.getDay() === 0 ? 'DOMINGO = DSR' : 'SÁBADO = COMPENSADO';
}

        // Exportar registros para PDF
        function exportarRegistrosPDF() {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();
            const usuarioSelecionado = document.getElementById('selecionarUsuario').value;
            const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
            const usuario = usuarios.find(u => u.nome === usuarioSelecionado);
            const registros = JSON.parse(localStorage.getItem('registros') || '[]');
            const registrosUsuario = registros.filter(r => r.usuario === usuarioSelecionado);

            // Título
            doc.setFontSize(16);
            doc.text('Registros do Usuário', 10, 10);

            // Nome e CPF
            doc.setFontSize(12);
            doc.text(`Nome: ${usuario.nomeCompleto || usuarioSelecionado}`, 10, 20);
            doc.text(`CPF: ${usuario.cpf || '-'}`, 10, 30);

            // Tabela de registros
            let y = 40;
            doc.setFontSize(10);
            const headers = ['Dia', 'Entrada', 'Pausa', 'Retorno', 'Saída'];
            const tableData = [];

            // Determinar o mês atual
            const hoje = new Date();
            const ano = hoje.getFullYear();
            const mes = hoje.getMonth();
            const ultimoDia = new Date(ano, mes + 1, 0).getDate();

// Preencher dados da tabela
for (let dia = 1; dia <= 31; dia++) {
    const data = new Date(ano, mes, dia);
    if (dia > ultimoDia) {
        tableData.push(['-', '-', '-', '-', '-']);
        continue;
    }

    const dataStr = data.toLocaleDateString('pt-BR', { day: '2-digit', month: '2-digit', year: 'numeric' });

    if (ehFimDeSemana(data)) {
        const rot = data.getDay() === 0 ? 'DSR' : 'COMPENSADO';
        tableData.push([dataStr, rot, rot, rot, rot]);
        continue;
    }

    const registrosDia = registrosUsuario.filter(r => new Date(r.dataHora).toLocaleDateString('pt-BR') === dataStr);
    const entrada = registrosDia.find(r => r.tipo === 'Entrada')?.dataHora;
    const pausa   = registrosDia.find(r => r.tipo === 'Pausa')?.dataHora;
    const retorno = registrosDia.find(r => r.tipo === 'Retorno')?.dataHora;
    const saida   = registrosDia.find(r => r.tipo === 'Saída')?.dataHora;

    tableData.push([
        dataStr,
        entrada ? new Date(entrada).toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }) : '-',
        pausa   ? new Date(pausa)  .toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }) : '-',
        retorno ? new Date(retorno).toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }) : '-',
        saida   ? new Date(saida)  .toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }) : '-'
    ]);
}

            // Desenhar tabela
            doc.autoTable({
                startY: y,
                head: [headers],
                body: tableData,
                theme: 'grid',
                styles: { fontSize: 8, cellPadding: 2 },
                headStyles: { fillColor: [40, 40, 40] },
                margin: { left: 10, right: 10 }
            });

            // Relatório
            y = doc.lastAutoTable.finalY + 10;
            doc.setFontSize(12);
            doc.text('Relatório', 10, y);
            y += 10;

            let totalHoras = 0;
            let totalPausas = 0;
            let ultimaEntrada = null;

            registrosUsuario.forEach(registro => {
                if (registro.tipo === 'Entrada' || registro.tipo === 'Retorno') {
                    ultimaEntrada = new Date(registro.dataHora);
                } else if (registro.tipo === 'Saída' && ultimaEntrada) {
                    const saida = new Date(registro.dataHora);
                    const horas = (saida - ultimaEntrada) / (1000 * 60 * 60);
                    totalHoras += horas;
                    ultimaEntrada = null;
                } else if (registro.tipo === 'Pausa') {
                    totalPausas++;
                }
            });

            doc.setFontSize(10);
            doc.text(`Usuário: ${usuarioSelecionado}`, 10, y); y += 6;
            doc.text(`Total de horas trabalhadas: ${totalHoras.toFixed(2)} horas`, 10, y); y += 6;
            doc.text(`Total de pausas: ${totalPausas}`, 10, y); y += 10;

            // Assinatura
            doc.text('______________________________', 80, y);
            doc.text('Assinatura do funcionário', 80, y + 6);

            // Salvar PDF
            doc.save(`registros_${usuarioSelecionado}.pdf`);
            mostrarNotificacao('Registros exportados como PDF com sucesso!', 'success');
        }

        // Editar registro
        function editarRegistro(index) {
            const registros = JSON.parse(localStorage.getItem('registros') || '[]');
            const usuarioSelecionado = document.getElementById('selecionarUsuario').value;
            const registrosUsuario = registros.filter(r => r.usuario === usuarioSelecionado);
            const registro = registrosUsuario[index];
            if (registro) {
                document.getElementById('editarDataHoraRegistro').value = new Date(registro.dataHora).toISOString().slice(0, 16);
                document.getElementById('editarTipoRegistro').value = registro.tipo;
                document.getElementById('indiceRegistro').value = index;
                new bootstrap.Modal(document.getElementById('modalEditarRegistro')).show();
            }
        }

        // Salvar edição de registro
        function salvarEdicaoRegistro() {
            const index = parseInt(document.getElementById('indiceRegistro').value);
            const dataHora = document.getElementById('editarDataHoraRegistro').value;
            const tipo = document.getElementById('editarTipoRegistro').value;
            const usuarioSelecionado = document.getElementById('selecionarUsuario').value;

            if (!dataHora || !tipo) {
                mostrarNotificacao('Preencha todos os campos.', 'danger');
                return;
            }

            const registros = JSON.parse(localStorage.getItem('registros') || '[]');
            const registrosUsuario = registros.filter(r => r.usuario === usuarioSelecionado);
            if (registrosUsuario[index]) {
                const globalIndex = registros.findIndex(r => r.usuario === usuarioSelecionado && r.dataHora === registrosUsuario[index].dataHora && r.tipo === registrosUsuario[index].tipo);
                registros[globalIndex].dataHora = new Date(dataHora).toISOString();
                registros[globalIndex].tipo = tipo;
                localStorage.setItem('registros', JSON.stringify(registros));
                mostrarNotificacao('Registro editado com sucesso!', 'success');
                carregarRegistrosUsuarioPerfil(usuarioSelecionado);
                bootstrap.Modal.getInstance(document.getElementById('modalEditarRegistro')).hide();
            } else {
                mostrarNotificacao('Registro não encontrado.', 'danger');
            }
        }

        // Excluir registro
        function excluirRegistro() {
            const index = parseInt(document.getElementById('indiceRegistro').value);
            const usuarioSelecionado = document.getElementById('selecionarUsuario').value;
            const registros = JSON.parse(localStorage.getItem('registros') || '[]');
            const registrosUsuario = registros.filter(r => r.usuario === usuarioSelecionado);
            if (registrosUsuario[index]) {
                const globalIndex = registros.findIndex(r => r.usuario === usuarioSelecionado && r.dataHora === registrosUsuario[index].dataHora && r.tipo === registrosUsuario[index].tipo);
                registros.splice(globalIndex, 1);
                localStorage.setItem('registros', JSON.stringify(registros));
                mostrarNotificacao('Registro excluído com sucesso!', 'success');
                carregarRegistrosUsuarioPerfil(usuarioSelecionado);
            } else {
                mostrarNotificacao('Registro não encontrado.', 'danger');
            }
            bootstrap.Modal.getInstance(document.getElementById('modalExcluirRegistro')).hide();
        }

        // Mostrar tela de login
        function mostrarTelaLogin() {
            document.getElementById('telaLogin').classList.remove('escondido');
            document.getElementById('telaInicialAdmin').classList.add('escondido');
            document.getElementById('telaCadastro').classList.add('escondido');
            document.getElementById('telaGerenciarUsuarios').classList.add('escondido');
            document.getElementById('telaPerfilUsuario').classList.add('escondido');
            document.getElementById('telaPassagens').classList.add('escondido');
            document.getElementById('telaPrincipal').classList.add('escondido');
        }

        // Mostrar tela inicial do admin
        function mostrarTelaInicialAdmin() {
            document.getElementById('telaLogin').classList.add('escondido');
            document.getElementById('telaInicialAdmin').classList.remove('escondido');
            document.getElementById('telaCadastro').classList.add('escondido');
            document.getElementById('telaGerenciarUsuarios').classList.add('escondido');
            document.getElementById('telaPerfilUsuario').classList.add('escondido');
            document.getElementById('telaPassagens').classList.add('escondido');
            document.getElementById('telaPrincipal').classList.add('escondido');
            const usuarioAtual = localStorage.getItem('usuarioAtual');
            document.getElementById('usuarioAdmin').textContent = usuarioAtual;
        }

        // Mostrar tela de cadastro
        function mostrarTelaCadastro() {
            const usuarioAtual = localStorage.getItem('usuarioAtual');
            const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
            if (!usuarioAtual || !usuarios.find(usuario => usuario.nome === usuarioAtual)?.ehAdmin) {
                mostrarNotificacao('Apenas administradores podem cadastrar usuários.', 'danger');
                if (usuarioAtual) mostrarTelaInicialAdmin();
                else mostrarTelaLogin();
                return;
            }
            document.getElementById('telaLogin').classList.add('escondido');
            document.getElementById('telaInicialAdmin').classList.add('escondido');
            document.getElementById('telaCadastro').classList.remove('escondido');
            document.getElementById('telaGerenciarUsuarios').classList.add('escondido');
            document.getElementById('telaPerfilUsuario').classList.add('escondido');
            document.getElementById('telaPassagens').classList.add('escondido');
            document.getElementById('telaPrincipal').classList.add('escondido');
        }

        // Mostrar tela de gerenciamento de usuários
        function mostrarTelaGerenciarUsuarios() {
            const usuarioAtual = localStorage.getItem('usuarioAtual');
            const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
            if (!usuarioAtual || !usuarios.find(usuario => usuario.nome === usuarioAtual)?.ehAdmin) {
                mostrarNotificacao('Apenas administradores podem gerenciar usuários.', 'danger');
                if (usuarioAtual) mostrarTelaInicialAdmin();
                else mostrarTelaLogin();
                return;
            }
            document.getElementById('telaLogin').classList.add('escondido');
            document.getElementById('telaInicialAdmin').classList.add('escondido');
            document.getElementById('telaCadastro').classList.add('escondido');
            document.getElementById('telaGerenciarUsuarios').classList.remove('escondido');
            document.getElementById('telaPerfilUsuario').classList.add('escondido');
            document.getElementById('telaPassagens').classList.add('escondido');
            document.getElementById('telaPrincipal').classList.add('escondido');
            carregarMenuUsuarios();
        }

        // Mostrar tela de perfil do usuário
        function mostrarTelaPerfilUsuario() {
            const usuarioSelecionado = document.getElementById('selecionarUsuario').value;
            if (!usuarioSelecionado) {
                return;
            }
            const usuarioAtual = localStorage.getItem('usuarioAtual');
            const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
            if (!usuarioAtual || !usuarios.find(usuario => usuario.nome === usuarioAtual)?.ehAdmin) {
                mostrarNotificacao('Apenas administradores podem editar usuários.', 'danger');
                if (usuarioAtual) mostrarTelaInicialAdmin();
                else mostrarTelaLogin();
                return;
            }
            document.getElementById('telaLogin').classList.add('escondido');
            document.getElementById('telaInicialAdmin').classList.add('escondido');
            document.getElementById('telaCadastro').classList.add('escondido');
            document.getElementById('telaGerenciarUsuarios').classList.add('escondido');
            document.getElementById('telaPerfilUsuario').classList.remove('escondido');
            document.getElementById('telaPassagens').classList.add('escondido');
            document.getElementById('telaPrincipal').classList.add('escondido');

            const usuario = usuarios.find(u => u.nome === usuarioSelecionado);
            if (usuario) {
                document.getElementById('editarNomeUsuario').value = usuario.nome;
                document.getElementById('editarNomeCompleto').value = usuario.nomeCompleto || '';
                document.getElementById('editarCpf').value = usuario.cpf || '';
                document.getElementById('editarDataNascimento').value = usuario.dataNascimento || '';
                document.getElementById('editarEndereco').value = usuario.endereco || '';
                document.getElementById('editarDataAdmissao').value = usuario.dataAdmissao || '';
                document.getElementById('editarCargo').value = usuario.cargo || '';
                document.getElementById('editarPisPasep').value = usuario.pisPasep || '';
                document.getElementById('editarCtps').value = usuario.ctps || '';
                document.getElementById('editarSenhaUsuario').value = usuario.senha;
                document.getElementById('editarEhAdmin').checked = usuario.ehAdmin;
                document.getElementById('nomeUsuarioOriginal').value = usuario.nome;
                const fotoPerfil = document.getElementById('fotoPerfil');
                const uploadFoto = document.getElementById('uploadFoto');
                uploadFoto.value = '';
                if (usuario.foto) {
                    fotoPerfil.src = usuario.foto;
                    fotoPerfil.classList.remove('escondido');
                } else {
                    fotoPerfil.src = '';
                    fotoPerfil.classList.add('escondido');
                }
                // Mostrar botão de exportar PDF apenas para administradores
                const botaoExportarPDF = document.getElementById('botaoExportarPDF');
                botaoExportarPDF.classList.toggle('escondido', !usuarios.find(u => u.nome === usuarioAtual)?.ehAdmin);
                carregarRegistrosUsuarioPerfil(usuario.nome);
            } else {
                mostrarNotificacao('Usuário não encontrado.', 'danger');
                mostrarTelaGerenciarUsuarios();
            }
        }

        // Carregar registros do usuário no perfil
        function carregarRegistrosUsuarioPerfil(nomeUsuario) {
            const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
            const usuario = usuarios.find(u => u.nome === nomeUsuario);
            const registros = JSON.parse(localStorage.getItem('registros') || '[]');
            const corpoTabela = document.getElementById('corpoRegistrosUsuario');
            corpoTabela.innerHTML = '';

            // Exibir nome completo e CPF
            document.getElementById('nomeCompletoUsuarioPerfil').textContent = usuario.nomeCompleto || nomeUsuario;
            document.getElementById('cpfUsuarioPerfil').textContent = usuario.cpf ? `CPF: ${usuario.cpf}` : 'CPF: -';

            // Filtrar registros do usuário
            const registrosUsuario = registros.filter(r => r.usuario === nomeUsuario);

            // Determinar o mês atual
            const hoje = new Date();
            const ano = hoje.getFullYear();
            const mes = hoje.getMonth(); // 0 = Janeiro, 11 = Dezembro
            const ultimoDia = new Date(ano, mes + 1, 0).getDate(); // Último dia do mês

// Criar tabela com 31 linhas
for (let dia = 1; dia <= 31; dia++) {
    const data = new Date(ano, mes, dia);
    const dataStr = data.toLocaleDateString('pt-BR', { day: '2-digit', month: '2-digit', year: 'numeric' });
    const linha = document.createElement('tr');

    // Dias além do último dia do mês
    if (dia > ultimoDia) {
        linha.innerHTML = `<td>-</td><td>-</td><td>-</td><td>-</td><td>-</td>`;
        corpoTabela.appendChild(linha);
        continue;
    }

    // Verifica se é fim de semana
    if (data.getDay() === 6) { // Sábado
        linha.classList.add('table-secondary');
        linha.innerHTML = `<td>${dataStr}</td><td colspan="4" class="text-center fw-bold">SÁBADO = COMPENSADO</td>`;
        corpoTabela.appendChild(linha);
        continue;
    } else if (data.getDay() === 0) { // Domingo
        linha.classList.add('table-secondary');
        linha.innerHTML = `<td>${dataStr}</td><td colspan="4" class="text-center fw-bold">DOMINGO = DSR</td>`;
        corpoTabela.appendChild(linha);
        continue;
    }

    // Dias úteis: preencher com registros do usuário
    const registrosDia = registrosUsuario.filter(r => new Date(r.dataHora).toLocaleDateString('pt-BR') === dataStr);
    const entrada = registrosDia.find(r => r.tipo === 'Entrada')?.dataHora;
    const pausa = registrosDia.find(r => r.tipo === 'Pausa')?.dataHora;
    const retorno = registrosDia.find(r => r.tipo === 'Retorno')?.dataHora;
    const saida = registrosDia.find(r => r.tipo === 'Saída')?.dataHora;

    linha.innerHTML = `
        <td>${dataStr}</td>
        <td>${entrada ? new Date(entrada).toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }) : '-'}</td>
        <td>${pausa ? new Date(pausa).toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }) : '-'}</td>
        <td>${retorno ? new Date(retorno).toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }) : '-'}</td>
        <td>${saida ? new Date(saida).toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }) : '-'}</td>
    `;
    corpoTabela.appendChild(linha);
}

            // Atualizar relatório
            let totalHoras = 0;
            let totalPausas = 0;
            let ultimaEntrada = null;

            registrosUsuario.forEach(registro => {
                if (registro.tipo === 'Entrada' || registro.tipo === 'Retorno') {
                    ultimaEntrada = new Date(registro.dataHora);
                } else if (registro.tipo === 'Saída' && ultimaEntrada) {
                    const saida = new Date(registro.dataHora);
                    const horas = (saida - ultimaEntrada) / (1000 * 60 * 60);
                    totalHoras += horas;
                    ultimaEntrada = null;
                } else if (registro.tipo === 'Pausa') {
                    totalPausas++;
                }
            });

            let relatorio = `Usuário: ${nomeUsuario}\n`;
            relatorio += `Total de horas trabalhadas: ${totalHoras.toFixed(2)} horas\n`;
            relatorio += `Total de pausas: ${totalPausas}\n`;

            document.getElementById('conteudoRelatorioUsuario').textContent = relatorio || 'Nenhum registro encontrado.';
        }

        // Mostrar tela de passagens
        function mostrarTelaPassagens() {
            const usuarioAtual = localStorage.getItem('usuarioAtual');
            const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
            if (!usuarioAtual || !usuarios.find(usuario => usuario.nome === usuarioAtual)?.ehAdmin) {
                mostrarNotificacao('Apenas administradores podem acessar passagens.', 'danger');
                if (usuarioAtual) mostrarTelaInicialAdmin();
                else mostrarTelaLogin();
                return;
            }
            document.getElementById('telaLogin').classList.add('escondido');
            document.getElementById('telaInicialAdmin').classList.add('escondido');
            document.getElementById('telaCadastro').classList.add('escondido');
            document.getElementById('telaGerenciarUsuarios').classList.add('escondido');
            document.getElementById('telaPerfilUsuario').classList.add('escondido');
            document.getElementById('telaPassagens').classList.remove('escondido');
            document.getElementById('telaPrincipal').classList.add('escondido');
        }

        // Mostrar tela principal (bater ponto)
        function mostrarTelaPrincipal() {
            document.getElementById('telaLogin').classList.add('escondido');
            document.getElementById('telaInicialAdmin').classList.add('escondido');
            document.getElementById('telaCadastro').classList.add('escondido');
            document.getElementById('telaGerenciarUsuarios').classList.add('escondido');
            document.getElementById('telaPerfilUsuario').classList.add('escondido');
            document.getElementById('telaPassagens').classList.add('escondido');
            document.getElementById('telaPrincipal').classList.remove('escondido');
            const usuarioAtual = localStorage.getItem('usuarioAtual');
            document.getElementById('usuarioAtual').textContent = usuarioAtual;
            const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
            const ehAdmin = usuarios.find(usuario => usuario.nome === usuarioAtual)?.ehAdmin;
            document.getElementById('botaoVoltarAdmin').classList.toggle('escondido', !ehAdmin);
            carregarRegistros();
        }

        // Preencher menu de usuários
        function carregarMenuUsuarios() {
            const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
            const selecionarUsuario = document.getElementById('selecionarUsuario');
            selecionarUsuario.innerHTML = '<option value="">Selecione um usuário</option>';
            usuarios.forEach(usuario => {
                const opcao = document.createElement('option');
                opcao.value = usuario.nome;
                opcao.textContent = usuario.nome;
                selecionarUsuario.appendChild(opcao);
            });
        }

        // Salvar edição de usuário
        function salvarEdicaoUsuario() {
            const nomeOriginal = document.getElementById('nomeUsuarioOriginal').value;
            const novoNome = document.getElementById('editarNomeUsuario').value.trim();
            const nomeCompleto = document.getElementById('editarNomeCompleto').value.trim();
            const cpf = document.getElementById('editarCpf').value.trim();
            const dataNascimento = document.getElementById('editarDataNascimento').value;
            const endereco = document.getElementById('editarEndereco').value.trim();
            const dataAdmissao = document.getElementById('editarDataAdmissao').value;
            const cargo = document.getElementById('editarCargo').value.trim();
            const pisPasep = document.getElementById('editarPisPasep').value.trim();
            const ctps = document.getElementById('editarCtps').value.trim();
            const novaSenha = document.getElementById('editarSenhaUsuario').value.trim();
            const ehAdmin = document.getElementById('editarEhAdmin').checked;
            const foto = document.getElementById('fotoPerfil').src;
            const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');

            if (!novoNome || !novaSenha) {
                mostrarNotificacao('Nome de usuário e senha são obrigatórios.', 'danger');
                return;
            }
            if (cpf && !validarCPF(cpf)) {
                mostrarNotificacao('CPF inválido. Deve conter 11 dígitos.', 'danger');
                return;
            }
            if (pisPasep && !validarPisPasep(pisPasep)) {
                mostrarNotificacao('PIS/PASEP inválido. Deve conter 11 dígitos.', 'danger');
                return;
            }
            if (novoNome !== nomeOriginal && usuarios.some(usuario => usuario.nome === novoNome)) {
                mostrarNotificacao('Nome de usuário já existe. Escolha outro nome.', 'danger');
                return;
            }

            const usuario = usuarios.find(u => u.nome === nomeOriginal);
            if (usuario) {
                const registros = JSON.parse(localStorage.getItem('registros') || '[]');
                registros.forEach(registro => {
                    if (registro.usuario === nomeOriginal) {
                        registro.usuario = novoNome;
                    }
                });
                localStorage.setItem('registros', JSON.stringify(registros));

                usuario.nome = novoNome;
                usuario.nomeCompleto = nomeCompleto;
                usuario.cpf = cpf;
                usuario.dataNascimento = dataNascimento;
                usuario.endereco = endereco;
                usuario.dataAdmissao = dataAdmissao;
                usuario.cargo = cargo;
                usuario.pisPasep = pisPasep;
                usuario.ctps = ctps;
                usuario.senha = novaSenha;
                usuario.ehAdmin = ehAdmin;
                usuario.foto = foto && !foto.includes('data:,') ? foto : '';
                localStorage.setItem('usuarios', JSON.stringify(usuarios));

                const usuarioAtual = localStorage.getItem('usuarioAtual');
                if (usuarioAtual === nomeOriginal) {
                    localStorage.setItem('usuarioAtual', novoNome);
                }

                mostrarNotificacao('Usuário editado com sucesso!', 'success');
                mostrarTelaGerenciarUsuarios();
            } else {
                mostrarNotificacao('Usuário não encontrado.', 'danger');
                mostrarTelaGerenciarUsuarios();
            }
        }

        // Excluir usuário
        function excluirUsuario() {
            const nomeOriginal = document.getElementById('nomeUsuarioOriginal').value;
            const usuarioAtual = localStorage.getItem('usuarioAtual');
            if (nomeOriginal === usuarioAtual) {
                mostrarNotificacao('Você não pode excluir o usuário atualmente logado.', 'danger');
                bootstrap.Modal.getInstance(document.getElementById('modalExcluirUsuario')).hide();
                return;
            }

            let usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
            usuarios = usuarios.filter(u => u.nome !== nomeOriginal);
            localStorage.setItem('usuarios', JSON.stringify(usuarios));

            let registros = JSON.parse(localStorage.getItem('registros') || '[]');
            registros = registros.filter(r => r.usuario !== nomeOriginal);
            localStorage.setItem('registros', JSON.stringify(registros));

            bootstrap.Modal.getInstance(document.getElementById('modalExcluirUsuario')).hide();
            mostrarNotificacao('Usuário excluído com sucesso!', 'success');
            mostrarTelaGerenciarUsuarios();
        }
// Cadastrar usuário
function cadastrar() {
    const usuarioAtual = localStorage.getItem('usuarioAtual');
    const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
    if (!usuarioAtual || !usuarios.find(usuario => usuario.nome === usuarioAtual)?.ehAdmin) {
        mostrarNotificacao('Apenas administradores podem cadastrar usuários.', 'danger');
        if (usuarioAtual) mostrarTelaInicialAdmin();
        else mostrarTelaLogin();
        return;
    }

    const nome = document.getElementById('nomeUsuarioCadastro').value.trim();
    const nomeCompleto = document.getElementById('nomeCompletoCadastro').value.trim();
    const cpf = document.getElementById('cpfCadastro').value.trim();
    const dataNascimento = document.getElementById('dataNascimentoCadastro').value;
    const endereco = document.getElementById('enderecoCadastro').value.trim();
    const dataAdmissao = document.getElementById('dataAdmissaoCadastro').value;
    const cargo = document.getElementById('cargoCadastro').value.trim();
    const pisPasep = document.getElementById('pisPasepCadastro').value.trim();
    const ctps = document.getElementById('ctpsCadastro').value.trim();
    const senha = document.getElementById('senhaCadastro').value.trim();
    const ehAdmin = document.getElementById('ehAdmin').checked;
    const uploadFoto = document.getElementById('uploadFotoCadastro');
    const foto = uploadFoto.files[0] ? document.getElementById('fotoPerfil').src : '';

    if (!nome || !senha) {
        mostrarNotificacao('Nome de usuário e senha são obrigatórios.', 'danger');
        return;
    }
    if (cpf && !validarCPF(cpf)) {
        mostrarNotificacao('CPF inválido. Deve conter 11 dígitos.', 'danger');
        return;
    }
    if (pisPasep && !validarPisPasep(pisPasep)) {
        mostrarNotificacao('PIS/PASEP inválido. Deve conter 11 dígitos.', 'danger');
        return;
    }
    if (usuarios.some(usuario => usuario.nome === nome)) {
        mostrarNotificacao('Usuário já existe. Escolha outro nome.', 'danger');
        return;
    }

    usuarios.push({
        nome,
        nomeCompleto,
        cpf,
        dataNascimento,
        endereco,
        dataAdmissao,
        cargo,
        pisPasep,
        ctps,
        senha,
        ehAdmin,
        foto
    });
    localStorage.setItem('usuarios', JSON.stringify(usuarios));
    mostrarNotificacao('Usuário cadastrado com sucesso!', 'success');
    mostrarTelaGerenciarUsuarios();
    document.getElementById('nomeUsuarioCadastro').value = '';
    document.getElementById('nomeCompletoCadastro').value = '';
    document.getElementById('cpfCadastro').value = '';
    document.getElementById('dataNascimentoCadastro').value = '';
    document.getElementById('enderecoCadastro').value = '';
    document.getElementById('dataAdmissaoCadastro').value = '';
    document.getElementById('cargoCadastro').value = '';
    document.getElementById('pisPasepCadastro').value = '';
    document.getElementById('ctpsCadastro').value = '';
    document.getElementById('senhaCadastro').value = '';
    document.getElementById('ehAdmin').checked = false;
    document.getElementById('uploadFotoCadastro').value = '';

    // 🔹 Sincroniza com Firebase
    if (window.registrarNoFirebase) {
        registrarNoFirebase(nome, senha).then(ok => {
            if (!ok) {
                mostrarNotificacao('Erro ao sincronizar com servidor.', 'danger');
            }
        });
    }
}


// Entrar
function entrar() {
    const nome = document.getElementById('nomeUsuarioLogin').value.trim();
    const senha = document.getElementById('senhaLogin').value.trim();
    console.log('Tentativa de login:', { nome, senha });
    if (!nome || !senha) {
        mostrarNotificacao('Por favor, preencha todos os campos.', 'danger');
        return;
    }

    const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
    console.log('Usuários no localStorage:', usuarios);
    const usuario = usuarios.find(usuario => usuario.nome === nome && usuario.senha === senha);
    if (!usuario) {
        mostrarNotificacao('Usuário ou senha incorretos.', 'danger');
        return;
    }

    localStorage.setItem('usuarioAtual', nome);
    console.log('Usuário logado:', nome);
    if (usuario.ehAdmin) {
        mostrarTelaInicialAdmin();
    } else {
        mostrarTelaPrincipal();
    }
    document.getElementById('nomeUsuarioLogin').value = '';
    document.getElementById('senhaLogin').value = '';

    // 🔹 Valida no Firebase
    if (window.loginNoFirebase) {
        loginNoFirebase(nome, senha).then(ok => {
            if (!ok) {
                mostrarNotificacao('Erro ao validar no servidor.', 'danger');
            }
        });
    }
}
      

        // Sair
        function sair() {
            localStorage.removeItem('usuarioAtual');
            mostrarTelaLogin();
        }

        // Carregar registros do usuário atual
        function carregarRegistros() {
            const usuarioAtual = localStorage.getItem('usuarioAtual');
            const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
            const usuario = usuarios.find(u => u.nome === usuarioAtual);
            const registros = JSON.parse(localStorage.getItem('registros') || '[]');
            const corpoTabela = document.getElementById('tabelasRegistros');
            corpoTabela.innerHTML = '';

            // Exibir nome completo e CPF
            document.getElementById('nomeCompletoUsuario').textContent = usuario.nomeCompleto || usuarioAtual;
            document.getElementById('cpfUsuario').textContent = usuario.cpf ? `CPF: ${usuario.cpf}` : 'CPF: -';

            // Filtrar registros do usuário
            const registrosUsuario = registros.filter(r => r.usuario === usuarioAtual);

            // Determinar o mês atual
            const hoje = new Date();
            const ano = hoje.getFullYear();
            const mes = hoje.getMonth(); // 0 = Janeiro, 11 = Dezembro
            const ultimoDia = new Date(ano, mes + 1, 0).getDate(); // Último dia do mês

// Criar tabela com 31 linhas
for (let dia = 1; dia <= 31; dia++) {
    const data = new Date(ano, mes, dia);
    const dataStr = data.toLocaleDateString('pt-BR', { day: '2-digit', month: '2-digit', year: 'numeric' });
    const linha = document.createElement('tr');

    // Dias além do último dia do mês
    if (dia > ultimoDia) {
        linha.innerHTML = `<td>-</td><td>-</td><td>-</td><td>-</td><td>-</td>`;
        corpoTabela.appendChild(linha);
        continue;
    }

    // Verifica se é fim de semana
    if (data.getDay() === 6) { // Sábado
        linha.classList.add('table-secondary');
        linha.innerHTML = `<td>${dataStr}</td><td colspan="4" class="text-center fw-bold">SÁBADO = COMPENSADO</td>`;
        corpoTabela.appendChild(linha);
        continue;
    } else if (data.getDay() === 0) { // Domingo
        linha.classList.add('table-secondary');
        linha.innerHTML = `<td>${dataStr}</td><td colspan="4" class="text-center fw-bold">DOMINGO = DSR</td>`;
        corpoTabela.appendChild(linha);
        continue;
    }

    // Dias úteis: preencher com registros do usuário
    const registrosDia = registrosUsuario.filter(r => new Date(r.dataHora).toLocaleDateString('pt-BR') === dataStr);
    const entrada = registrosDia.find(r => r.tipo === 'Entrada')?.dataHora;
    const pausa = registrosDia.find(r => r.tipo === 'Pausa')?.dataHora;
    const retorno = registrosDia.find(r => r.tipo === 'Retorno')?.dataHora;
    const saida = registrosDia.find(r => r.tipo === 'Saída')?.dataHora;

    linha.innerHTML = `
        <td>${dataStr}</td>
        <td>${entrada ? new Date(entrada).toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }) : '-'}</td>
        <td>${pausa ? new Date(pausa).toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }) : '-'}</td>
        <td>${retorno ? new Date(retorno).toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }) : '-'}</td>
        <td>${saida ? new Date(saida).toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }) : '-'}</td>
    `;
    corpoTabela.appendChild(linha);
}

            // Atualizar relatório
            atualizarRelatorio();
        }

        // Registrar ponto com validação
        function registrarPonto(tipo) {
            const usuario = localStorage.getItem('usuarioAtual');
            if (!usuario) {
                mostrarNotificacao('Por favor, faça login novamente.', 'danger');
                mostrarTelaLogin();
                return;
            }

            const registros = JSON.parse(localStorage.getItem('registros') || '[]');
            const ultimoRegistro = registros.filter(r => r.usuario === usuario).sort((a, b) => new Date(b.dataHora) - new Date(a.dataHora))[0];

            if (ultimoRegistro) {
                if ((tipo === 'Entrada' || tipo === 'Retorno') && (ultimoRegistro.tipo === 'Entrada' || ultimoRegistro.tipo === 'Retorno')) {
                    mostrarNotificacao(`Você já registrou uma ${ultimoRegistro.tipo}. Registre uma Pausa ou Saída.`, 'danger');
                    return;
                }
                if (tipo === 'Pausa' && ultimoRegistro.tipo === 'Pausa') {
                    mostrarNotificacao('Você já está em Pausa. Registre um Retorno.', 'danger');
                    return;
                }
                if (tipo === 'Retorno' && ultimoRegistro.tipo !== 'Pausa') {
                    mostrarNotificacao('Retorno só pode ser registrado após uma Pausa.', 'danger');
                    return;
                }
                if (tipo === 'Saída' && (ultimoRegistro.tipo === 'Saída' || ultimoRegistro.tipo === 'Pausa' || !ultimoRegistro)) {
                    mostrarNotificacao('Você precisa registrar uma Entrada ou Retorno antes de uma Saída.', 'danger');
                    return;
                }
            } else if (tipo !== 'Entrada') {
                mostrarNotificacao('O primeiro registro deve ser uma Entrada.', 'danger');
                return;
            }

            const agora = new Date();
            const registro = { usuario, dataHora: agora.toISOString(), tipo };
            registros.push(registro);
            localStorage.setItem('registros', JSON.stringify(registros));

            mostrarNotificacao(`Ponto de ${tipo} registrado com sucesso!`, 'success');
            carregarRegistros();
        }

        // Atualizar relatório
        function atualizarRelatorio() {
            const registros = JSON.parse(localStorage.getItem('registros') || '[]');
            const usuarioAtual = localStorage.getItem('usuarioAtual');
            const registrosUsuario = registros.filter(r => r.usuario === usuarioAtual);
            let totalHoras = 0;
            let totalPausas = 0;
            let ultimaEntrada = null;

            registrosUsuario.forEach(registro => {
                if (registro.tipo === 'Entrada' || registro.tipo === 'Retorno') {
                    ultimaEntrada = new Date(registro.dataHora);
                } else if (registro.tipo === 'Saída' && ultimaEntrada) {
                    const saida = new Date(registro.dataHora);
                    const horas = (saida - ultimaEntrada) / (1000 * 60 * 60);
                    totalHoras += horas;
                    ultimaEntrada = null;
                } else if (registro.tipo === 'Pausa') {
                    totalPausas++;
                }
            });

            let relatorio = `Usuário: ${usuarioAtual}\n`;
            relatorio += `Total de horas trabalhadas: ${totalHoras.toFixed(2)} horas\n`;
            relatorio += `Total de pausas: ${totalPausas}\n`;

            document.getElementById('conteudoRelatorio').textContent = relatorio || 'Nenhum registro encontrado.';
        }

        // Exportar para CSV
        function exportarParaCSV() {
            const registros = JSON.parse(localStorage.getItem('registros') || '[]');
            const usuarioAtual = localStorage.getItem('usuarioAtual');
            const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
            const ehAdmin = usuarios.find(usuario => usuario.nome === usuarioAtual)?.ehAdmin;
            const registrosUsuario = ehAdmin ? registros : registros.filter(r => r.usuario === usuarioAtual);
            if (registrosUsuario.length === 0) {
                mostrarNotificacao('Nenhum registro para exportar.', 'danger'); return; }
let csv = 'Usuário,Data,Hora,Tipo\n';
        registrosUsuario.forEach(registro => {
            const dataHora = new Date(registro.dataHora);
            const data = dataHora.toLocaleDateString('pt-BR');
            const hora = dataHora.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' });
            csv += `${registro.usuario},${data},${hora},${registro.tipo}\n`;
        });

        const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
        const link = document.createElement('a');
        const url = URL.createObjectURL(blob);
        link.setAttribute('href', url);
        link.setAttribute('download', `registros_${usuarioAtual}.csv`);
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        mostrarNotificacao('Registros exportados como CSV com sucesso!', 'success');
    }

    // Limpar registros
    function limparRegistros() {
        const usuarioAtual = localStorage.getItem('usuarioAtual');
        let registros = JSON.parse(localStorage.getItem('registros') || '[]');
        const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
        const ehAdmin = usuarios.find(usuario => usuario.nome === usuarioAtual)?.ehAdmin;

        if (ehAdmin) {
            registros = [];
        } else {
            registros = registros.filter(r => r.usuario !== usuarioAtual);
        }

        localStorage.setItem('registros', JSON.stringify(registros));
        mostrarNotificacao('Registros limpos com sucesso!', 'success');
        carregarRegistros();
        bootstrap.Modal.getInstance(document.getElementById('modalLimpar')).hide();
    }

    // Inicializar
    (async function inicializar() {
    await carregarUsuariosFirebase(); // Busca usuários no Firestore antes de tudo
    const usuarioAtual = localStorage.getItem('usuarioAtual');
    if (usuarioAtual) {
        const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
        const ehAdmin = usuarios.find(usuario => usuario.nome === usuarioAtual)?.ehAdmin;
        if (ehAdmin) {
            mostrarTelaInicialAdmin();
        } else {
            mostrarTelaPrincipal();
        }
    } else {
        mostrarTelaLogin();
    }
})();

    // Carregar Bootstrap JS para modais e toasts
    document.addEventListener('DOMContentLoaded', () => {
        const script = document.createElement('script');
        script.src = 'https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js';
        script.integrity = 'sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz';
        script.crossOrigin = 'anonymous';
        document.body.appendChild(script);
    });
    </script>
    
    <!-- Firebase SDK -->
<script type="module">
  // Importando Firebase
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-app.js";
  import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-auth.js";
  import { getFirestore, collection, getDocs, doc, deleteDoc } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-firestore.js";

  // Configuração do Firebase
  const firebaseConfig = {
    apiKey: "AIzaSyCwHEYAAWzuArnjHKyBB2HQornlGXgXcwc",
    authDomain: "admz-4cc94.firebaseapp.com",
    projectId: "admz-4cc94",
    storageBucket: "admz-4cc94.firebasestorage.app",
    messagingSenderId: "1058002382869",
    appId: "1:1058002382869:web:01e295c26aab0c662b1e1c",
    measurementId: "G-WFJ4D9JFGC"
  };

  // Inicializa Firebase
  const app = initializeApp(firebaseConfig);
  const auth = getAuth(app);
  const db = getFirestore(app);

  // Criar usuário no Firebase
  async function registrarNoFirebase(usuario, senha) {
    const emailFake = `${usuario}@admz.app`;
    try {
      await createUserWithEmailAndPassword(auth, emailFake, senha);
      console.log("Usuário registrado no Firebase:", usuario);
      return true;
    } catch (error) {
      if (error.code === "auth/email-already-in-use") {
        console.warn("Usuário já existe no Firebase.");
        return true;
      }
      console.error("Erro ao registrar no Firebase:", error);
      return false;
    }
  }

  // Login no Firebase
  async function loginNoFirebase(usuario, senha) {
    const emailFake = `${usuario}@admz.app`;
    try {
      await signInWithEmailAndPassword(auth, emailFake, senha);
      console.log("Login no Firebase bem-sucedido:", usuario);
      return true;
    } catch (error) {
      console.error("Erro no login Firebase:", error);
      return false;
    }
  }

  // Carregar usuários do Firebase
  async function carregarUsuariosFirebase() {
    try {
      const querySnapshot = await getDocs(collection(db, "usuarios"));
      const usuarios = [];
      querySnapshot.forEach((doc) => {
        usuarios.push(doc.data());
      });
      localStorage.setItem('usuarios', JSON.stringify(usuarios));
      console.log("Usuários carregados do Firebase:", usuarios);
    } catch (error) {
      console.error("Erro ao carregar usuários do Firebase:", error);
    }
  }

  // Excluir usuário no Firebase
  async function excluirUsuarioFirebase(nome) {
    try {
      await deleteDoc(doc(db, "usuarios", nome));
      console.log(`Usuário ${nome} removido do Firestore`);
      return true;
    } catch (error) {
      console.error("Erro ao excluir usuário do Firebase:", error);
      return false;
    }
  }

  // Tornando funções globais
  window.registrarNoFirebase = registrarNoFirebase;
  window.loginNoFirebase = loginNoFirebase;
  window.carregarUsuariosFirebase = carregarUsuariosFirebase;
  window.excluirUsuarioFirebase = excluirUsuarioFirebase;
</script>
</body>
</html>
