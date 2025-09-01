<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Controle de Ponto</title>
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-..." crossorigin="anonymous">
<style>
.escondido { display: none; }
</style>
</head>
<body>

<!-- Suas telas HTML aqui: telaLogin, telaInicialAdmin, telaCadastro, telaGerenciarUsuarios, telaPerfilUsuario, telaPassagens, telaPrincipal -->
<!-- Por simplicidade, não repeti o HTML das telas, apenas o JS corrigido será mostrado -->

<script type="module">
/* ===================== FUNÇÕES DE NAVEGAÇÃO ENTRE TELAS ===================== */
function mostrarTelaLogin() {
    document.getElementById('telaLogin').classList.remove('escondido');
    document.getElementById('telaInicialAdmin').classList.add('escondido');
    document.getElementById('telaCadastro').classList.add('escondido');
    document.getElementById('telaGerenciarUsuarios').classList.add('escondido');
    document.getElementById('telaPerfilUsuario').classList.add('escondido');
    document.getElementById('telaPassagens').classList.add('escondido');
    document.getElementById('telaPrincipal').classList.add('escondido');
}

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

function mostrarTelaCadastro() {
    const usuarioAtual = localStorage.getItem('usuarioAtual');
    const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
    if (!usuarioAtual || !usuarios.find(u => u.nome === usuarioAtual)?.ehAdmin) {
        mostrarNotificacao('Apenas administradores podem cadastrar usuários.', 'danger');
        usuarioAtual ? mostrarTelaInicialAdmin() : mostrarTelaLogin();
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

function mostrarTelaGerenciarUsuarios() {
    const usuarioAtual = localStorage.getItem('usuarioAtual');
    const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
    if (!usuarioAtual || !usuarios.find(u => u.nome === usuarioAtual)?.ehAdmin) {
        mostrarNotificacao('Apenas administradores podem gerenciar usuários.', 'danger');
        usuarioAtual ? mostrarTelaInicialAdmin() : mostrarTelaLogin();
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

function mostrarTelaPerfilUsuario() {
    const usuarioSelecionado = document.getElementById('selecionarUsuario').value;
    if (!usuarioSelecionado) return;

    const usuarioAtual = localStorage.getItem('usuarioAtual');
    const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
    if (!usuarioAtual || !usuarios.find(u => u.nome === usuarioAtual)?.ehAdmin) {
        mostrarNotificacao('Apenas administradores podem editar usuários.', 'danger');
        usuarioAtual ? mostrarTelaInicialAdmin() : mostrarTelaLogin();
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

        // Botão de exportar PDF apenas para admin
        const botaoExportarPDF = document.getElementById('botaoExportarPDF');
        botaoExportarPDF.classList.toggle('escondido', !usuarios.find(u => u.nome === usuarioAtual)?.ehAdmin);

        carregarRegistrosUsuarioPerfil(usuario.nome);
    } else {
        mostrarNotificacao('Usuário não encontrado.', 'danger');
        mostrarTelaGerenciarUsuarios();
    }
}

/* ===================== RESTANTE DAS FUNÇÕES ===================== */
/* Todas as funções de cadastro, login, registrar ponto, exportar CSV, limpar registros, carregar usuários do Firebase, etc., continuam exatamente como você enviou, já corrigidas com as observações anteriores */

</script>

<!-- Firebase SDK -->
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-app.js";
import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-auth.js";
import { getFirestore, collection, getDocs, doc, deleteDoc } from "https://www.gstatic.com/firebasejs/10.8.1/firebase-firestore.js";

// Config Firebase
const firebaseConfig = {
    apiKey: "AIzaSyCwHEYAAWzuArnjHKyBB2HQornlGXgXcwc",
    authDomain: "admz-4cc94.firebaseapp.com",
    projectId: "admz-4cc94",
    storageBucket: "admz-4cc94.firebasestorage.app",
    messagingSenderId: "1058002382869",
    appId: "1:1058002382869:web:01e295c26aab0c662b1e1c",
    measurementId: "G-WFJ4D9JFGC"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);

async function registrarNoFirebase(usuario, senha) {
    const emailFake = `${usuario}@admz.app`;
    try {
        await createUserWithEmailAndPassword(auth, emailFake, senha);
        console.log("Usuário registrado no Firebase:", usuario);
        return true;
    } catch (error) {
        if (error.code === "auth/email-already-in-use") return true;
        console.error("Erro ao registrar no Firebase:", error);
        return false;
    }
}

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

async function carregarUsuariosFirebase() {
    try {
        const querySnapshot = await getDocs(collection(db, "usuarios"));
        const usuarios = [];
        querySnapshot.forEach(doc => usuarios.push(doc.data()));
        localStorage.setItem('usuarios', JSON.stringify(usuarios));
        console.log("Usuários carregados do Firebase:", usuarios);
    } catch (error) {
        console.error("Erro ao carregar usuários do Firebase:", error);
    }
}

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

// Tornar funções globais
window.registrarNoFirebase = registrarNoFirebase;
window.loginNoFirebase = loginNoFirebase;
window.carregarUsuariosFirebase = carregarUsuariosFirebase;
window.excluirUsuarioFirebase = excluirUsuarioFirebase;

/* ===================== INICIALIZAÇÃO ===================== */
(async function inicializar() {
    await carregarUsuariosFirebase();
    const usuarioAtual = localStorage.getItem('usuarioAtual');
    if (usuarioAtual) {
        const usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]');
        const ehAdmin = usuarios.find(u => u.nome === usuarioAtual)?.ehAdmin;
        ehAdmin ? mostrarTelaInicialAdmin() : mostrarTelaPrincipal();
    } else {
        mostrarTelaLogin();
    }
})();

</script>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-..." crossorigin="anonymous"></script>
</body>
</html>
