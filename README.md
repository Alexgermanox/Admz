<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ponto Eletrônico</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f4f4f4;
        }
        .container {
            max-width: 800px;
            margin: 20px auto;
            padding: 15px;
            background: white;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        .section {
            display: none;
        }
        .section.active {
            display: block;
        }
        h2 {
            color: #333;
            font-size: 1.5em;
            margin-bottom: 10px;
        }
        input, select, button {
            padding: 8px;
            margin: 5px 0;
            width: calc(100% - 16px);
            box-sizing: border-box;
            font-size: 0.9em;
        }
        button {
            background: #007BFF;
            color: white;
            border: none;
            cursor: pointer;
            border-radius: 4px;
            padding: 8px 12px;
            margin-right: 5px;
            display: inline-block;
        }
        button:hover {
            background: #0056b3;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin: 15px 0;
            overflow-x: auto;
            display: block;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: left;
            font-size: 0.85em;
        }
        th {
            background: #f2f2f2;
        }
        .error {
            color: red;
            font-size: 0.8em;
        }
        .success {
            color: green;
            font-size: 0.8em;
        }
        .assinatura {
            margin-top: 20px;
            font-size: 0.9em;
        }
        @media (max-width: 600px) {
            .container {
                margin: 10px;
                padding: 10px;
            }
            button {
                width: auto;
                margin: 5px;
            }
            .button-group {
                display: flex;
                flex-wrap: wrap;
                gap: 5px;
            }
            table {
                font-size: 0.8em;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Login -->
        <div id="login" class="section active">
            <h2>Entrar</h2>
            <input type="text" id="loginUsername" placeholder="Nome de Usuário" required>
            <input type="password" id="loginPassword" placeholder="Senha" required>
            <div class="button-group">
                <button onclick="login()">Entrar</button>
            </div>
            <p class="error" id="loginError"></p>
        </div>

        <!-- Admin Dashboard -->
        <div id="adminDashboard" class="section">
            <h2>Painel do Administrador</h2>
            <p>Bem-vindo, <span id="adminName"></span></p>
            <div class="button-group">
                <button onclick="showSection('timeTracking')">Bater Ponto</button>
                <button onclick="showSection('manageUsers')">Gerenciar Usuários</button>
                <button onclick="showSection('passages')">Passagens</button>
                <button onclick="logout()">Sair</button>
            </div>
        </div>

        <!-- Register User -->
        <div id="registerUser" class="section">
            <h2>Cadastrar Usuário</h2>
            <input type="text" id="regUsername" placeholder="Nome de Usuário">
            <input type="text" id="regFullName" placeholder="Nome Completo">
            <input type="text" id="regCPF" placeholder="CPF">
            <input type="date" id="regBirthDate" placeholder="Data de Nascimento">
            <input type="text" id="regAddress" placeholder="Endereço">
            <input type="date" id="regAdmissionDate" placeholder="Data de Admissão">
            <input type="text" id="regPosition" placeholder="Cargo">
            <input type="text" id="regPIS" placeholder="PIS/PASEP">
            <input type="text" id="regCTPS" placeholder="CTPS">
            <input type="password" id="regPassword" placeholder="Senha">
            <label><input type="checkbox" id="regIsAdmin"> Administrador</label>
            <div class="button-group">
                <button onclick="registerUser()">Cadastrar</button>
                <button onclick="showSection('manageUsers')">Voltar</button>
            </div>
            <p class="success" id="registerSuccess"></p>
            <p class="error" id="registerError"></p>
        </div>

        <!-- Manage Users -->
        <div id="manageUsers" class="section">
            <h2>Gerenciar Usuários</h2>
            <div class="button-group">
                <button onclick="showSection('registerUser')">Cadastrar Novo Usuário</button>
            </div>
            <h3>Usuários</h3>
            <select id="userSelect" onchange="viewUserProfile()">
                <option value="">Selecione um usuário</option>
            </select>
            <div class="button-group">
                <button onclick="showSection('adminDashboard')">Voltar</button>
            </div>
        </div>

        <!-- User Profile -->
        <div id="userProfile" class="section">
            <h2>Perfil do Usuário</h2>
            <input type="file" id="profilePhoto" accept="image/*">
            <div class="button-group">
                <button onclick="removePhoto()">Remover Foto</button>
            </div>
            <input type="text" id="profileUsername" placeholder="Nome de Usuário">
            <input type="text" id="profileFullName" placeholder="Nome Completo">
            <input type="text" id="profileCPF" placeholder="CPF">
            <input type="date" id="profileBirthDate" placeholder="Data de Nascimento">
            <input type="text" id="profileAddress" placeholder="Endereço">
            <input type="date" id="profileAdmissionDate" placeholder="Data de Admissão">
            <input type="text" id="profilePosition" placeholder="Cargo">
            <input type="text" id="profilePIS" placeholder="PIS/PASEP">
            <input type="text" id="profileCTPS" placeholder="CTPS">
            <input type="password" id="profilePassword" placeholder="Senha">
            <label><input type="checkbox" id="profileIsAdmin"> Administrador</label>
            <div class="button-group">
                <button onclick="exportUserProfilePDF()">Exportar PDF</button>
                <button onclick="saveUserProfile()">Salvar</button>
                <button onclick="showDeleteUserConfirm()">Excluir Usuário</button>
                <button onclick="showSection('manageUsers')">Voltar</button>
                <button onclick="showSection('userRecords')">Ver Registros</button>
            </div>
        </div>

        <!-- User Records -->
        <div id="userRecords" class="section">
            <h2>Registros do Usuário</h2>
            <div class="button-group">
                <button onclick="exportRecordsPDF()">Exportar PDF</button>
            </div>
            <table>
                <thead>
                    <tr>
                        <th>Dia</th>
                        <th>Entrada</th>
                        <th>Pausa</th>
                        <th>Retorno</th>
                        <th>Saída</th>
                        <th>Ação</th>
                    </tr>
                </thead>
                <tbody id="recordsTable"></tbody>
            </table>
            <h3>Relatório</h3>
            <p id="reportSummary"></p>
            <div class="assinatura">Assinatura do funcionário: ____________________</div>
            <div class="button-group">
                <button onclick="showSection('userProfile')">Voltar</button>
            </div>
        </div>

        <!-- Edit Record -->
        <div id="editRecord" class="section">
            <h2>Editar Registro</h2>
            <input type="datetime-local" id="editRecordDateTime">
            <select id="editRecordType">
                <option value="Entrada">Entrada</option>
                <option value="Pausa">Pausa</option>
                <option value="Retorno">Retorno</option>
                <option value="Saída">Saída</option>
            </select>
            <div class="button-group">
                <button onclick="cancelEditRecord()">Cancelar</button>
                <button onclick="saveRecord()">Salvar</button>
            </div>
        </div>

        <!-- Delete Record Confirmation -->
        <div id="deleteRecordConfirm" class="section">
            <h2>Confirmação</h2>
            <p>Tem certeza que deseja excluir este registro? Esta ação não pode ser desfeita.</p>
            <div class="button-group">
                <button onclick="cancelDeleteRecord()">Cancelar</button>
                <button onclick="deleteRecord()">Excluir</button>
            </div>
        </div>

        <!-- Delete User Confirmation -->
        <div id="deleteUserConfirm" class="section">
            <h2>Confirmação</h2>
            <p>Tem certeza que deseja excluir este usuário? Esta ação não pode ser desfeita.</p>
            <div class="button-group">
                <button onclick="showSection('userProfile')">Cancelar</button>
                <button onclick="deleteUser()">Excluir</button>
            </div>
        </div>

        <!-- Passages -->
        <div id="passages" class="section">
            <h2>Passagens</h2>
            <p>Funcionalidade em desenvolvimento.</p>
            <div class="button-group">
                <button onclick="showSection('adminDashboard')">Voltar</button>
            </div>
        </div>

        <!-- Time Tracking -->
        <div id="timeTracking" class="section">
            <h2>Bater Ponto</h2>
            <p>Usuário: <span id="trackingUsername"></span></p>
            <p>Horário atual: <span id="currentTime"></span></p>
            <div class="button-group">
                <button onclick="recordTime('Entrada')">Entrada</button>
                <button onclick="recordTime('Pausa')">Pausa</button>
                <button onclick="recordTime('Retorno')">Retorno</button>
                <button onclick="recordTime('Saída')">Saída</button>
                <button onclick="showSection('adminDashboard')">Voltar ao Painel</button>
                <button onclick="exportRecordsCSV()">Exportar CSV</button>
                <button onclick="showClearRecordsConfirm()">Limpar Registros</button>
                <button onclick="logout()">Sair</button>
                <button onclick="showSection('myRecords')">Meus Registros</button>
            </div>
        </div>

        <!-- My Records -->
        <div id="myRecords" class="section">
            <h2>Meus Registros</h2>
            <table>
                <thead>
                    <tr>
                        <th>Dia</th>
                        <th>Entrada</th>
                        <th>Pausa</th>
                        <th>Retorno</th>
                        <th>Saída</th>
                    </tr>
                </thead>
                <tbody id="myRecordsTable"></tbody>
            </table>
            <h3>Relatório</h3>
            <p id="myReportSummary"></p>
            <div class="assinatura">Assinatura do funcionário: ____________________</div>
            <div class="button-group">
                <button onclick="showSection('timeTracking')">Voltar</button>
            </div>
        </div>

        <!-- Clear Records Confirmation -->
        <div id="clearRecordsConfirm" class="section">
            <h2>Confirmação</h2>
            <p>Tem certeza que deseja limpar todos os registros? Esta ação não pode ser desfeita.</p>
            <div class="button-group">
                <button onclick="showSection('timeTracking')">Cancelar</button>
                <button onclick="clearRecords()">Limpar</button>
            </div>
        </div>
    </div>

    <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-storage.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script>
        const { jsPDF } = window.jspdf;
        let currentUser = null;
        let editingRecord = null;
        let db, auth, storage;

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

        // Verificar se os scripts do Firebase estão carregados
        function checkFirebaseScripts() {
            if (typeof firebase === 'undefined') {
                console.error('Erro: Firebase SDK não carregado');
                document.getElementById('loginError').textContent = 'Erro: Firebase SDK não carregado. Verifique sua conexão ou tente novamente.';
                return false;
            }
            console.log('Firebase SDK carregado');
            return true;
        }

        // Inicializar Firebase e serviços
        function initializeFirebase() {
            return new Promise((resolve, reject) => {
                if (!checkFirebaseScripts()) {
                    reject(new Error('Firebase SDK não carregado'));
                    return;
                }
                try {
                    firebase.initializeApp(firebaseConfig);
                    db = firebase.firestore();
                    auth = firebase.auth();
                    storage = firebase.storage();
                    console.log('Firebase inicializado com sucesso');
                    resolve();
                } catch (error) {
                    console.error('Erro ao inicializar Firebase:', error);
                    document.getElementById('loginError').textContent = `Erro ao inicializar Firebase: ${error.message}`;
                    reject(error);
                }
            });
        }

        // Validação de CPF
        function validarCPF(cpf) {
            cpf = cpf.replace(/[^\d]+/g, '');
            if (cpf.length !== 11) return false;
            let soma = 0;
            for (let i = 0; i < 9; i++) soma += parseInt(cpf.charAt(i)) * (10 - i);
            let resto = (soma * 10) % 11;
            if (resto === 10 || resto === 11) resto = 0;
            if (resto !== parseInt(cpf.charAt(9))) return false;
            soma = 0;
            for (let i = 0; i < 10; i++) soma += parseInt(cpf.charAt(i)) * (11 - i);
            resto = (soma * 10) % 11;
            if (resto === 10 || resto === 11) resto = 0;
            return resto === parseInt(cpf.charAt(10));
        }

        // Inicializar administrador padrão
        async function initializeAdmin() {
            if (!db || !auth) {
                console.error('Firestore ou Authentication não inicializado');
                return;
            }
            try {
                const adminRef = db.collection('usuarios').doc('admin');
                const adminDoc = await adminRef.get();
                if (!adminDoc.exists) {
                    console.log('Criando usuário admin...');
                    try {
                        await auth.createUserWithEmailAndPassword('admin@admz.app', 'admin123');
                        await adminRef.set({
                            username: 'admin',
                            fullName: 'Administrador',
                            cpf: '',
                            birthDate: '',
                            address: '',
                            admissionDate: '',
                            position: 'Administrador',
                            pis: '',
                            ctps: '',
                            password: 'admin123',
                            isAdmin: true,
                            photo: null
                        });
                        console.log('Administrador criado com sucesso no Firestore');
                    } catch (error) {
                        if (error.code === 'auth/email-already-in-use') {
                            console.log('Usuário admin já existe no Authentication');
                        } else {
                            console.error('Erro ao criar administrador:', error.code, error.message);
                        }
                    }
                } else {
                    console.log('Usuário admin já existe no Firestore');
                }
            } catch (error) {
                console.error('Erro ao verificar administrador:', error.code, error.message);
            }
        }

        // Login
        async function login() {
            if (!auth) {
                document.getElementById('loginError').textContent = 'Erro: Firebase Authentication não inicializado';
                console.error('Firebase Authentication não inicializado');
                return;
            }
            const username = document.getElementById('loginUsername').value.trim();
            const password = document.getElementById('loginPassword').value;
            const email = `${username}@admz.app`;
            console.log('Tentando login com:', email, 'Senha:', password);
            try {
                const userCredential = await auth.signInWithEmailAndPassword(email, password);
                console.log('Autenticação bem-sucedida, UID:', userCredential.user.uid);
                const userDoc = await db.collection('usuarios').doc(username).get();
                if (userDoc.exists) {
                    currentUser = userDoc.data();
                    document.getElementById('adminName').textContent = currentUser.fullName;
                    document.getElementById('trackingUsername').textContent = currentUser.fullName;
                    console.log('Login bem-sucedido, usuário:', currentUser);
                    showSection(currentUser.isAdmin ? 'adminDashboard' : 'timeTracking');
                } else {
                    document.getElementById('loginError').textContent = 'Usuário não encontrado no Firestore';
                    console.error('Usuário não encontrado no Firestore:', username);
                    if (username === 'admin') {
                        console.log('Recriando documento admin no Firestore...');
                        await db.collection('usuarios').doc('admin').set({
                            username: 'admin',
                            fullName: 'Administrador',
                            cpf: '',
                            birthDate: '',
                            address: '',
                            admissionDate: '',
                            position: 'Administrador',
                            pis: '',
                            ctps: '',
                            password: 'admin123',
                            isAdmin: true,
                            photo: null
                        });
                        console.log('Documento admin recriado, tentando login novamente...');
                        const retryDoc = await db.collection('usuarios').doc(username).get();
                        if (retryDoc.exists) {
                            currentUser = retryDoc.data();
                            document.getElementById('adminName').textContent = currentUser.fullName;
                            document.getElementById('trackingUsername').textContent = currentUser.fullName;
                            showSection(currentUser.isAdmin ? 'adminDashboard' : 'timeTracking');
                        }
                    }
                }
            } catch (error) {
                document.getElementById('loginError').textContent = `Erro: ${error.message}`;
                console.error('Erro no login:', error.code, error.message);
                if (error.code === 'auth/user-not-found' && username === 'admin') {
                    console.log('Usuário admin não encontrado, tentando criar...');
                    try {
                        await auth.createUserWithEmailAndPassword('admin@admz.app', 'admin123');
                        await db.collection('usuarios').doc('admin').set({
                            username: 'admin',
                            fullName: 'Administrador',
                            cpf: '',
                            birthDate: '',
                            address: '',
                            admissionDate: '',
                            position: 'Administrador',
                            pis: '',
                            ctps: '',
                            password: 'admin123',
                            isAdmin: true,
                            photo: null
                        });
                        console.log('Usuário admin criado, tentando login novamente...');
                        await auth.signInWithEmailAndPassword('admin@admz.app', 'admin123');
                        const userDoc = await db.collection('usuarios').doc('admin').get();
                        if (userDoc.exists) {
                            currentUser = userDoc.data();
                            document.getElementById('adminName').textContent = currentUser.fullName;
                            document.getElementById('trackingUsername').textContent = currentUser.fullName;
                            showSection('adminDashboard');
                        }
                    } catch (createError) {
                        console.error('Erro ao criar admin:', createError.code, createError.message);
                        document.getElementById('loginError').textContent = `Erro ao criar admin: ${createError.message}`;
                    }
                }
            }
        }

        // Logout
        async function logout() {
            if (!auth) {
                console.error('Firebase Authentication não inicializado');
                return;
            }
            try {
                await auth.signOut();
                currentUser = null;
                showSection('login');
                document.getElementById('loginUsername').value = '';
                document.getElementById('loginPassword').value = '';
                console.log('Logout bem-sucedido');
            } catch (error) {
                console.error('Erro ao fazer logout:', error.code, error.message);
            }
        }

        // Cadastrar usuário
        async function registerUser() {
            if (!auth || !db) {
                document.getElementById('registerError').textContent = 'Erro: Firebase não inicializado';
                console.error('Firebase não inicializado');
                return;
            }
            const user = {
                username: document.getElementById('regUsername').value.trim(),
                fullName: document.getElementById('regFullName').value,
                cpf: document.getElementById('regCPF').value,
                birthDate: document.getElementById('regBirthDate').value,
                address: document.getElementById('regAddress').value,
                admissionDate: document.getElementById('regAdmissionDate').value,
                position: document.getElementById('regPosition').value,
                pis: document.getElementById('regPIS').value,
                ctps: document.getElementById('regCTPS').value,
                password: document.getElementById('regPassword').value,
                isAdmin: document.getElementById('regIsAdmin').checked,
                photo: null
            };
            if (!user.username || !user.fullName || !user.password) {
                document.getElementById('registerError').textContent = 'Preencha os campos obrigatórios';
                return;
            }
            if (user.cpf && !validarCPF(user.cpf)) {
                document.getElementById('registerError').textContent = 'CPF inválido';
                return;
            }
            try {
                const userDoc = await db.collection('usuarios').doc(user.username).get();
                if (userDoc.exists) {
                    document.getElementById('registerError').textContent = 'Usuário já existe';
                    return;
                }
                await auth.createUserWithEmailAndPassword(`${user.username}@admz.app`, user.password);
                await db.collection('usuarios').doc(user.username).set(user);
                document.getElementById('registerSuccess').textContent = 'Usuário cadastrado com sucesso';
                console.log('Usuário cadastrado:', user.username);
                setTimeout(() => showSection('manageUsers'), 1000);
            } catch (error) {
                document.getElementById('registerError').textContent = error.message;
                console.error('Erro no cadastro:', error.code, error.message);
            }
        }

        // Carregar lista de usuários
        async function loadUsers() {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            const select = document.getElementById('userSelect');
            select.innerHTML = '<option value="">Selecione um usuário</option>';
            try {
                const snapshot = await db.collection('usuarios').get();
                snapshot.forEach(doc => {
                    const user = doc.data();
                    const option = document.createElement('option');
                    option.value = user.username;
                    option.textContent = user.fullName;
                    select.appendChild(option);
                });
                console.log('Usuários carregados com sucesso');
            } catch (error) {
                console.error('Erro ao carregar usuários:', error.code, error.message);
            }
        }

        // Carregar perfil do usuário
        async function viewUserProfile() {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            const username = document.getElementById('userSelect').value;
            if (!username) return;
            try {
                const userDoc = await db.collection('usuarios').doc(username).get();
                if (userDoc.exists) {
                    const user = userDoc.data();
                    document.getElementById('profileUsername').value = user.username;
                    document.getElementById('profileFullName').value = user.fullName;
                    document.getElementById('profileCPF').value = user.cpf;
                    document.getElementById('profileBirthDate').value = user.birthDate;
                    document.getElementById('profileAddress').value = user.address;
                    document.getElementById('profileAdmissionDate').value = user.admissionDate;
                    document.getElementById('profilePosition').value = user.position;
                    document.getElementById('profilePIS').value = user.pis;
                    document.getElementById('profileCTPS').value = user.ctps;
                    document.getElementById('profilePassword').value = user.password;
                    document.getElementById('profileIsAdmin').checked = user.isAdmin;
                    showSection('userProfile');
                    console.log('Perfil carregado:', username);
                }
            } catch (error) {
                console.error('Erro ao carregar perfil:', error.code, error.message);
            }
        }

        // Salvar perfil do usuário
        async function saveUserProfile() {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            const username = document.getElementById('profileUsername').value;
            const cpf = document.getElementById('profileCPF').value;
            if (cpf && !validarCPF(cpf)) {
                alert('CPF inválido');
                return;
            }
            try {
                await db.collection('usuarios').doc(username).update({
                    fullName: document.getElementById('profileFullName').value,
                    cpf: cpf,
                    birthDate: document.getElementById('profileBirthDate').value,
                    address: document.getElementById('profileAddress').value,
                    admissionDate: document.getElementById('profileAdmissionDate').value,
                    position: document.getElementById('profilePosition').value,
                    pis: document.getElementById('profilePIS').value,
                    ctps: document.getElementById('profileCTPS').value,
                    password: document.getElementById('profilePassword').value,
                    isAdmin: document.getElementById('profileIsAdmin').checked
                });
                alert('Perfil atualizado com sucesso');
                console.log('Perfil atualizado:', username);
                showSection('manageUsers');
            } catch (error) {
                console.error('Erro ao salvar perfil:', error.code, error.message);
            }
        }

        // Confirmação de exclusão de usuário
        function showDeleteUserConfirm() {
            showSection('deleteUserConfirm');
        }

        // Excluir usuário
        async function deleteUser() {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            const username = document.getElementById('profileUsername').value;
            try {
                await db.collection('usuarios').doc(username).delete();
                const records = await db.collection('registros').where('username', '==', username).get();
                const batch = db.batch();
                records.forEach(doc => batch.delete(doc.ref));
                await batch.commit();
                alert('Usuário excluído com sucesso');
                console.log('Usuário excluído:', username);
                showSection('manageUsers');
            } catch (error) {
                console.error('Erro ao excluir usuário:', error.code, error.message);
            }
        }

        // Atualizar horário atual
        function updateCurrentTime() {
            const now = new Date();
            document.getElementById('currentTime').textContent = now.toLocaleString('pt-BR');
            setTimeout(updateCurrentTime, 1000);
        }

        // Registrar ponto
        async function recordTime(type) {
            if (!currentUser) {
                alert('Usuário não logado');
                return;
            }
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            const date = new Date();
            const dayOfWeek = date.getDay();
            if (dayOfWeek === 0 || dayOfWeek === 6) {
                alert('Não é possível registrar ponto em finais de semana');
                return;
            }
            try {
                await db.collection('registros').add({
                    username: currentUser.username,
                    date: date.toISOString().split('T')[0],
                    time: date.toLocaleTimeString('pt-BR'),
                    type
                });
                alert(`${type} registrada com sucesso`);
                console.log('Ponto registrado:', type);
            } catch (error) {
                console.error('Erro ao registrar ponto:', error.code, error.message);
            }
        }

        // Carregar registros do usuário
        async function loadUserRecords() {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            const username = document.getElementById('userSelect').value;
            const table = document.getElementById('recordsTable');
            table.innerHTML = '';
            try {
                const snapshot = await db.collection('registros').where('username', '==', username).get();
                const records = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                const groupedRecords = groupRecordsByDate(records);
                const allDates = getAllDates();
                allDates.forEach(date => {
                    const dayOfWeek = new Date(date).getDay();
                    const row = document.createElement('tr');
                    if (dayOfWeek === 6) {
                        row.innerHTML = `<td>${date}</td><td colspan="4">Sábado = COMPENSADO</td><td></td>`;
                    } else if (dayOfWeek === 0) {
                        row.innerHTML = `<td>${date}</td><td colspan="4">Domingo = DSR</td><td></td>`;
                    } else {
                        row.innerHTML = `
                            <td>${date}</td>
                            <td>${groupedRecords[date]?.Entrada || ''}</td>
                            <td>${groupedRecords[date]?.Pausa || ''}</td>
                            <td>${groupedRecords[date]?.Retorno || ''}</td>
                            <td>${groupedRecords[date]?.Saída || ''}</td>
                            <td><button onclick="editRecord('${username}', '${date}', 'Entrada')">Editar</button></td>
                        `;
                    }
                    table.appendChild(row);
                });
                updateReportSummary(records, 'reportSummary');
                console.log('Registros do usuário carregados:', username);
            } catch (error) {
                console.error('Erro ao carregar registros:', error.code, error.message);
            }
        }

        // Carregar meus registros
        async function loadMyRecords() {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            const table = document.getElementById('myRecordsTable');
            table.innerHTML = '';
            try {
                const snapshot = await db.collection('registros').where('username', '==', currentUser.username).get();
                const records = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                const groupedRecords = groupRecordsByDate(records);
                const allDates = getAllDates();
                allDates.forEach(date => {
                    const dayOfWeek = new Date(date).getDay();
                    const row = document.createElement('tr');
                    if (dayOfWeek === 6) {
                        row.innerHTML = `<td>${date}</td><td colspan="4">Sábado = COMPENSADO</td>`;
                    } else if (dayOfWeek === 0) {
                        row.innerHTML = `<td>${date}</td><td colspan="4">Domingo = DSR</td>`;
                    } else {
                        row.innerHTML = `
                            <td>${date}</td>
                            <td>${groupedRecords[date]?.Entrada || ''}</td>
                            <td>${groupedRecords[date]?.Pausa || ''}</td>
                            <td>${groupedRecords[date]?.Retorno || ''}</td>
                            <td>${groupedRecords[date]?.Saída || ''}</td>
                        `;
                    }
                    table.appendChild(row);
                });
                updateReportSummary(records, 'myReportSummary');
                console.log('Meus registros carregados');
            } catch (error) {
                console.error('Erro ao carregar meus registros:', error.code, error.message);
            }
        }

        // Agrupar registros por data
        function groupRecordsByDate(records) {
            const grouped = {};
            records.forEach(r => {
                if (!grouped[r.date]) grouped[r.date] = {};
                grouped[r.date][r.type] = r.time;
            });
            return grouped;
        }

        // Obter todas as datas (últimos 30 dias)
        function getAllDates() {
            const dates = [];
            const today = new Date();
            for (let i = 29; i >= 0; i--) {
                const date = new Date(today);
                date.setDate(today.getDate() - i);
                dates.push(date.toISOString().split('T')[0]);
            }
            return dates;
        }

        // Atualizar resumo do relatório
        function updateReportSummary(records, elementId) {
            const totalHours = records.reduce((sum, r) => {
                if (r.type === 'Saída' && records.some(rec => rec.date === r.date && rec.type === 'Entrada')) {
                    const entry = records.find(rec => rec.date === r.date && rec.type === 'Entrada');
                    const exit = new Date(`${r.date} ${r.time}`);
                    const start = new Date(`${r.date} ${entry.time}`);
                    return sum + (exit - start) / (1000 * 60 * 60);
                }
                return sum;
            }, 0);
            document.getElementById(elementId).textContent = `Total de horas trabalhadas: ${totalHours.toFixed(2)} horas`;
        }

        // Editar registro
        async function editRecord(username, date, type) {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            try {
                const snapshot = await db.collection('registros').where('username', '==', username).where('date', '==', date).where('type', '==', type).get();
                if (!snapshot.empty) {
                    editingRecord = { id: snapshot.docs[0].id, ...snapshot.docs[0].data() };
                    document.getElementById('editRecordDateTime').value = `${editingRecord.date}T${editingRecord.time}`;
                    document.getElementById('editRecordType').value = editingRecord.type;
                    showSection('editRecord');
                    console.log('Editando registro:', editingRecord);
                }
            } catch (error) {
                console.error('Erro ao editar registro:', error.code, error.message);
            }
        }

        // Salvar registro
        async function saveRecord() {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            try {
                const dateTime = document.getElementById('editRecordDateTime').value;
                await db.collection('registros').doc(editingRecord.id).update({
                    date: dateTime.split('T')[0],
                    time: dateTime.split('T')[1],
                    type: document.getElementById('editRecordType').value
                });
                alert('Registro atualizado com sucesso');
                console.log('Registro atualizado:', editingRecord.id);
                showSection('userRecords');
            } catch (error) {
                console.error('Erro ao salvar registro:', error.code, error.message);
            }
        }

        // Cancelar edição de registro
        function cancelEditRecord() {
            showSection('userRecords');
        }

        // Confirmação de exclusão de registro
        function showDeleteRecordConfirm() {
            showSection('deleteRecordConfirm');
        }

        // Excluir registro
        async function deleteRecord() {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            try {
                await db.collection('registros').doc(editingRecord.id).delete();
                alert('Registro excluído com sucesso');
                console.log('Registro excluído:', editingRecord.id);
                showSection('userRecords');
            } catch (error) {
                console.error('Erro ao excluir registro:', error.code, error.message);
            }
        }

        // Cancelar exclusão de registro
        function cancelDeleteRecord() {
            showSection('userRecords');
        }

        // Confirmação de limpar registros
        function showClearRecordsConfirm() {
            showSection('clearRecordsConfirm');
        }

        // Limpar registros
        async function clearRecords() {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            try {
                const snapshot = await db.collection('registros').where('username', '==', currentUser.username).get();
                const batch = db.batch();
                snapshot.forEach(doc => batch.delete(doc.ref));
                await batch.commit();
                alert('Registros limpos com sucesso');
                console.log('Registros limpos para:', currentUser.username);
                showSection('timeTracking');
            } catch (error) {
                console.error('Erro ao limpar registros:', error.code, error.message);
            }
        }

        // Exportar registros em PDF
        async function exportRecordsPDF() {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            const username = document.getElementById('userSelect').value;
            try {
                const snapshot = await db.collection('registros').where('username', '==', username).get();
                const records = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                const doc = new jsPDF();
                doc.text(`Registros do Usuário: ${username}`, 10, 10);
                let y = 20;
                const groupedRecords = groupRecordsByDate(records);
                const allDates = getAllDates();
                allDates.forEach(date => {
                    const dayOfWeek = new Date(date).getDay();
                    if (dayOfWeek === 6) {
                        doc.text(`${date}: Sábado = COMPENSADO`, 10, y);
                    } else if (dayOfWeek === 0) {
                        doc.text(`${date}: Domingo = DSR`, 10, y);
                    } else {
                        doc.text(`${date}: Entrada: ${groupedRecords[date]?.Entrada || ''}, Pausa: ${groupedRecords[date]?.Pausa || ''}, Retorno: ${groupedRecords[date]?.Retorno || ''}, Saída: ${groupedRecords[date]?.Saída || ''}`, 10, y);
                    }
                    y += 10;
                });
                doc.text(`Total de horas trabalhadas: ${records.reduce((sum, r) => {
                    if (r.type === 'Saída' && records.some(rec => rec.date === r.date && rec.type === 'Entrada')) {
                        const entry = records.find(rec => rec.date === r.date && rec.type === 'Entrada');
                        const exit = new Date(`${r.date} ${r.time}`);
                        const start = new Date(`${r.date} ${entry.time}`);
                        return sum + (exit - start) / (1000 * 60 * 60);
                    }
                    return sum;
                }, 0).toFixed(2)} horas`, 10, y + 10);
                doc.text('Assinatura do funcionário: ____________________', 10, y + 20);
                doc.save(`registros_${username}.pdf`);
                console.log('PDF exportado:', username);
            } catch (error) {
                console.error('Erro ao exportar PDF:', error.code, error.message);
            }
        }

        // Exportar perfil do usuário em PDF
        function exportUserProfilePDF() {
            const username = document.getElementById('profileUsername').value;
            const doc = new jsPDF();
            doc.text(`Perfil do Usuário: ${username}`, 10, 10);
            doc.text(`Nome Completo: ${document.getElementById('profileFullName').value}`, 10, 20);
            doc.text(`CPF: ${document.getElementById('profileCPF').value}`, 10, 30);
            doc.text(`Data de Nascimento: ${document.getElementById('profileBirthDate').value}`, 10, 40);
            doc.text(`Endereço: ${document.getElementById('profileAddress').value}`, 10, 50);
            doc.text(`Data de Admissão: ${document.getElementById('profileAdmissionDate').value}`, 10, 60);
            doc.text(`Cargo: ${document.getElementById('profilePosition').value}`, 10, 70);
            doc.text(`PIS/PASEP: ${document.getElementById('profilePIS').value}`, 10, 80);
            doc.text(`CTPS: ${document.getElementById('profileCTPS').value}`, 10, 90);
            doc.text(`Administrador: ${document.getElementById('profileIsAdmin').checked ? 'Sim' : 'Não'}`, 10, 100);
            doc.save(`perfil_${username}.pdf`);
            console.log('Perfil exportado em PDF:', username);
        }

        // Exportar registros em CSV
        async function exportRecordsCSV() {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            try {
                const snapshot = await db.collection('registros').where('username', '==', currentUser.username).get();
                const records = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                const groupedRecords = groupRecordsByDate(records);
                const allDates = getAllDates();
                let csv = 'Dia,Entrada,Pausa,Retorno,Saída\n';
                allDates.forEach(date => {
                    const dayOfWeek = new Date(date).getDay();
                    if (dayOfWeek === 6) {
                        csv += `${date},,,,"Sábado = COMPENSADO"\n`;
                    } else if (dayOfWeek === 0) {
                        csv += `${date},,,,"Domingo = DSR"\n`;
                    } else {
                        csv += `${date},${groupedRecords[date]?.Entrada || ''},${groupedRecords[date]?.Pausa || ''},${groupedRecords[date]?.Retorno || ''},${groupedRecords[date]?.Saída || ''}\n`;
                    }
                });
                const blob = new Blob([csv], { type: 'text/csv' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `registros_${currentUser.username}.csv`;
                a.click();
                URL.revokeObjectURL(url);
                console.log('CSV exportado:', currentUser.username);
            } catch (error) {
                console.error('Erro ao exportar CSV:', error.code, error.message);
            }
        }

        // Remover foto
        async function removePhoto() {
            if (!db) {
                console.error('Firestore não inicializado');
                return;
            }
            const username = document.getElementById('profileUsername').value;
            try {
                await db.collection('usuarios').doc(username).update({ photo: null });
                alert('Foto removida com sucesso');
                console.log('Foto removida:', username);
            } catch (error) {
                console.error('Erro ao remover foto:', error.code, error.message);
            }
        }

        // Mostrar seção
        function showSection(sectionId) {
            document.querySelectorAll('.section').forEach(section => section.classList.remove('active'));
            const section = document.getElementById(sectionId);
            if (section) section.classList.add('active');
            if (sectionId === 'manageUsers') loadUsers();
            if (sectionId === 'userProfile') loadUserProfile();
            if (sectionId === 'userRecords') loadUserRecords();
            if (sectionId === 'myRecords') loadMyRecords();
            if (sectionId === 'timeTracking') updateCurrentTime();
            document.getElementById('loginError').textContent = '';
            document.getElementById('registerSuccess').textContent = '';
            document.getElementById('registerError').textContent = '';
        }

        // Inicializar aplicativo
        async function initApp() {
            try {
                await initializeFirebase();
                await initializeAdmin();
                showSection('login');
                console.log('Aplicativo inicializado com sucesso');
            } catch (error) {
                console.error('Erro ao inicializar aplicativo:', error);
                document.getElementById('loginError').textContent = `Erro ao inicializar aplicativo: ${error.message}`;
            }
        }

        // Iniciar aplicativo após o carregamento dos scripts
        window.onload = function() {
            if (checkFirebaseScripts()) {
                initApp();
            } else {
                document.getElementById('loginError').textContent = 'Erro: Firebase SDK não carregado. Verifique sua conexão.';
            }
        };
    </script>
</body>
</html>
