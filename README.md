<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ponto Eletrônico</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 0; padding: 0; background-color: #f4f4f4; }
        .container { max-width: 800px; margin: 20px auto; padding: 20px; background: white; border-radius: 8px; }
        .section { display: none; }
        .section.active { display: block; }
        h2 { color: #333; }
        input, select, button { padding: 8px; margin: 5px 0; width: calc(100% - 16px); box-sizing: border-box; }
        button { background: #007BFF; color: white; border: none; cursor: pointer; }
        button:hover { background: #0056b3; }
        table { width: 100%; border-collapse: collapse; margin: 20px 0; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
        th { background: #f2f2f2; }
        .error { color: red; }
        .success { color: green; }
        @media (max-width: 600px) {
            button { width: auto; display: inline-block; margin-right: 10px; }
            .container { padding: 10px; }
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
            <button onclick="login()">Entrar</button>
            <p class="error" id="loginError"></p>
        </div>

        <!-- Admin Dashboard -->
        <div id="adminDashboard" class="section">
            <h2>Painel do Administrador</h2>
            <p>Bem-vindo, <span id="adminName"></span></p>
            <button onclick="showSection('timeTracking')">Bater Ponto</button>
            <button onclick="showSection('manageUsers')">Gerenciar Usuários</button>
            <button onclick="showSection('passages')">Passagens</button>
            <button onclick="logout()">Sair</button>
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
            <button onclick="registerUser()">Cadastrar</button>
            <button onclick="showSection('manageUsers')">Voltar</button>
            <p class="success" id="registerSuccess"></p>
            <p class="error" id="registerError"></p>
        </div>

        <!-- Manage Users -->
        <div id="manageUsers" class="section">
            <h2>Gerenciar Usuários</h2>
            <button onclick="showSection('registerUser')">Cadastrar Novo Usuário</button>
            <h3>Usuários</h3>
            <select id="userSelect" onchange="viewUserProfile()">
                <option value="">Selecione um usuário</option>
            </select>
            <button onclick="showSection('adminDashboard')">Voltar</button>
        </div>

        <!-- User Profile -->
        <div id="userProfile" class="section">
            <h2>Perfil do Usuário</h2>
            <input type="file" id="profilePhoto" accept="image/*">
            <button onclick="removePhoto()">Remover Foto</button>
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
            <button onclick="exportUserProfilePDF()">Exportar PDF</button>
            <button onclick="saveUserProfile()">Salvar</button>
            <button onclick="showDeleteUserConfirm()">Excluir Usuário</button>
            <button onclick="showSection('manageUsers')">Voltar</button>
            <button onclick="showSection('userRecords')">Ver Registros</button>
        </div>

        <!-- User Records -->
        <div id="userRecords" class="section">
            <h2>Registros do Usuário</h2>
            <button onclick="exportRecordsPDF()">Exportar PDF</button>
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
            <button onclick="showSection('userProfile')">Voltar</button>
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
            <button onclick="cancelEditRecord()">Cancelar</button>
            <button onclick="saveRecord()">Salvar</button>
        </div>

        <!-- Delete Record Confirmation -->
        <div id="deleteRecordConfirm" class="section">
            <h2>Confirmação</h2>
            <p>Tem certeza que deseja excluir este registro? Esta ação não pode ser desfeita.</p>
            <button onclick="cancelDeleteRecord()">Cancelar</button>
            <button onclick="deleteRecord()">Excluir</button>
        </div>

        <!-- Delete User Confirmation -->
        <div id="deleteUserConfirm" class="section">
            <h2>Confirmação</h2>
            <p>Tem certeza que deseja excluir este usuário? Esta ação não pode ser desfeita.</p>
            <button onclick="showSection('userProfile')">Cancelar</button>
            <button onclick="deleteUser()">Excluir</button>
        </div>

        <!-- Passages -->
        <div id="passages" class="section">
            <h2>Passagens</h2>
            <p>Funcionalidade em desenvolvimento.</p>
            <button onclick="showSection('adminDashboard')">Voltar</button>
        </div>

        <!-- Time Tracking -->
        <div id="timeTracking" class="section">
            <h2>Bater Ponto</h2>
            <p>Usuário: <span id="trackingUsername"></span></p>
            <p>Horário atual: <span id="currentTime"></span></p>
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
            <button onclick="showSection('timeTracking')">Voltar</button>
        </div>

        <!-- Clear Records Confirmation -->
        <div id="clearRecordsConfirm" class="section">
            <h2>Confirmação</h2>
            <p>Tem certeza que deseja limpar todos os registros? Esta ação não pode ser desfeita.</p>
            <button onclick="showSection('timeTracking')">Cancelar</button>
            <button onclick="clearRecords()">Limpar</button>
        </div>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script>
        const { jsPDF } = window.jspdf;
        let currentUser = null;
        let editingRecord = null;

        // Inicializar administrador padrão
        function initializeAdmin() {
            const users = JSON.parse(localStorage.getItem('users') || '[]');
            if (!users.some(u => u.username === 'admin')) {
                users.push({
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
                localStorage.setItem('users', JSON.stringify(users));
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

        // Login
        function login() {
            const username = document.getElementById('loginUsername').value.trim();
            const password = document.getElementById('loginPassword').value;
            const users = JSON.parse(localStorage.getItem('users') || '[]');
            const user = users.find(u => u.username === username && u.password === password);
            if (user) {
                currentUser = user;
                document.getElementById('adminName').textContent = user.fullName;
                document.getElementById('trackingUsername').textContent = user.fullName;
                showSection(user.isAdmin ? 'adminDashboard' : 'timeTracking');
            } else {
                document.getElementById('loginError').textContent = 'Usuário ou senha inválidos';
            }
        }

        // Logout
        function logout() {
            currentUser = null;
            showSection('login');
            document.getElementById('loginUsername').value = '';
            document.getElementById('loginPassword').value = '';
        }

        // Cadastrar usuário
        function registerUser() {
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
            const users = JSON.parse(localStorage.getItem('users') || '[]');
            if (users.some(u => u.username === user.username)) {
                document.getElementById('registerError').textContent = 'Usuário já existe';
                return;
            }
            users.push(user);
            localStorage.setItem('users', JSON.stringify(users));
            document.getElementById('registerSuccess').textContent = 'Usuário cadastrado com sucesso';
            setTimeout(() => showSection('manageUsers'), 1000);
        }

        // Carregar lista de usuários
        function loadUsers() {
            const select = document.getElementById('userSelect');
            select.innerHTML = '<option value="">Selecione um usuário</option>';
            const users = JSON.parse(localStorage.getItem('users') || '[]');
            users.forEach(user => {
                const option = document.createElement('option');
                option.value = user.username;
                option.textContent = user.fullName;
                select.appendChild(option);
            });
        }

        // Carregar perfil do usuário
        function viewUserProfile() {
            const username = document.getElementById('userSelect').value;
            if (!username) return;
            const users = JSON.parse(localStorage.getItem('users') || '[]');
            const user = users.find(u => u.username === username);
            if (user) {
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
            }
        }

        // Salvar perfil do usuário
        function saveUserProfile() {
            const username = document.getElementById('profileUsername').value;
            const users = JSON.parse(localStorage.getItem('users') || '[]');
            const index = users.findIndex(u => u.username === username);
            if (index !== -1) {
                users[index] = {
                    username,
                    fullName: document.getElementById('profileFullName').value,
                    cpf: document.getElementById('profileCPF').value,
                    birthDate: document.getElementById('profileBirthDate').value,
                    address: document.getElementById('profileAddress').value,
                    admissionDate: document.getElementById('profileAdmissionDate').value,
                    position: document.getElementById('profilePosition').value,
                    pis: document.getElementById('profilePIS').value,
                    ctps: document.getElementById('profileCTPS').value,
                    password: document.getElementById('profilePassword').value,
                    isAdmin: document.getElementById('profileIsAdmin').checked,
                    photo: users[index].photo
                };
                localStorage.setItem('users', JSON.stringify(users));
                alert('Perfil atualizado com sucesso');
                showSection('manageUsers');
            }
        }

        // Confirmação de exclusão de usuário
        function showDeleteUserConfirm() {
            showSection('deleteUserConfirm');
        }

        // Excluir usuário
        function deleteUser() {
            const username = document.getElementById('profileUsername').value;
            let users = JSON.parse(localStorage.getItem('users') || '[]');
            users = users.filter(u => u.username !== username);
            localStorage.setItem('users', JSON.stringify(users));
            let records = JSON.parse(localStorage.getItem('records') || '[]');
            records = records.filter(r => r.username !== username);
            localStorage.setItem('records', JSON.stringify(records));
            alert('Usuário excluído com sucesso');
            showSection('manageUsers');
        }

        // Atualizar horário atual
        function updateCurrentTime() {
            const now = new Date();
            document.getElementById('currentTime').textContent = now.toLocaleString('pt-BR');
            setTimeout(updateCurrentTime, 1000);
        }

        // Registrar ponto
        function recordTime(type) {
            if (!currentUser) {
                alert('Usuário não logado');
                return;
            }
            const records = JSON.parse(localStorage.getItem('records') || '[]');
            const date = new Date();
            const dayOfWeek = date.getDay();
            if (dayOfWeek === 0 || dayOfWeek === 6) {
                alert('Não é possível registrar ponto em finais de semana');
                return;
            }
            records.push({
                username: currentUser.username,
                date: date.toISOString().split('T')[0],
                time: date.toLocaleTimeString('pt-BR'),
                type
            });
            localStorage.setItem('records', JSON.stringify(records));
            alert(`${type} registrada com sucesso`);
        }

        // Carregar registros do usuário
        function loadUserRecords() {
            const username = document.getElementById('userSelect').value;
            const records = JSON.parse(localStorage.getItem('records') || '[]').filter(r => r.username === username);
            const table = document.getElementById('recordsTable');
            table.innerHTML = '';
            const groupedRecords = groupRecordsByDate(records);
            const allDates = getAllDates();
            allDates.forEach(date => {
                const dayOfWeek = new Date(date).getDay();
                const row = document.createElement('tr');
                if (dayOfWeek === 6) { // Sábado
                    row.innerHTML = `<td>${date}</td><td colspan="4">Sábado = COMPENSADO</td><td></td>`;
                } else if (dayOfWeek === 0) { // Domingo
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
        }

        // Carregar meus registros
        function loadMyRecords() {
            const records = JSON.parse(localStorage.getItem('records') || '[]').filter(r => r.username === currentUser.username);
            const table = document.getElementById('myRecordsTable');
            table.innerHTML = '';
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

        // Obter todas as datas (exemplo: últimos 30 dias)
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
            document.getElementById(elementId).textContent = `Total de horas trabalhKits de ferramentas sugeridos para tornar seu sistema web funcional online:

1. **Hospedagem Online**: Para hospedar o sistema online, você pode usar plataformas gratuitas como **GitHub Pages** ou **Netlify**. Aqui está como fazer isso:

   - **GitHub Pages**:
     1. Crie um repositório no GitHub.
     2. Faça upload do arquivo `index.html` para o repositório.
     3. Ative o GitHub Pages nas configurações do repositório, escolhendo a branch principal.
     4. O site estará disponível em `https://<seu-usuario>.github.io/<nome-do-repositorio>`.

   - **Netlify**:
     1. Crie uma conta no Netlify.
     2. Conecte seu repositório GitHub ou faça upload do arquivo `index.html`.
     3. Configure o site e obtenha um link como `https://<seu-site>.netlify.app`.

   Ambas as opções são gratuitas e fáceis de usar. O sistema funcionará online, mas os dados ainda serão armazenados no localStorage do navegador do usuário. Para persistência de dados entre dispositivos, você precisará de um backend (ex.: Firebase).

2. **Validações Adicionais**: Com base nas suas mensagens anteriores, você mencionou interesse em validações, como para CPF. Aqui está um exemplo de validação de CPF no cadastro:

   ```javascript
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

   // Adicionar na função registerUser
   function registerUser() {
       const cpf = document.getElementById('regCPF').value;
       if (!validarCPF(cpf)) {
           document.getElementById('registerError').textContent = 'CPF inválido';
           return;
       }
       // Restante do código...
   }
   ```

3. **Integração com Firebase**: Você mencionou anteriormente interesse em usar Firebase para salvar cadastros. Para isso, você precisa:
   - Criar um projeto no Firebase (https://console.firebase.google.com).
   - Obter as credenciais (API Key, Auth Domain, etc.).
   - Adicionar o SDK do Firebase ao seu projeto:

   ```html
   <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js"></script>
   <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js"></script>
   <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js"></script>
   ```

   - Configurar o Firebase no JavaScript:

   ```javascript
   const firebaseConfig = {
       apiKey: "SUA_API_KEY",
       authDomain: "SEU_PROJETO.firebaseapp.com",
       projectId: "SEU_PROJETO",
       // Outras configurações
   };
   firebase.initializeApp(firebaseConfig);
   const db = firebase.firestore();

   // Exemplo: salvar usuário no Firestore
   function registerUser() {
       const user = {
           username: document.getElementById('regUsername').value.trim(),
           fullName: document.getElementById('regFullName').value,
           // Outros campos
           email: `${document.getElementById('regUsername').value}@admz.app`, // Email fake
           password: document.getElementById('regPassword').value
       };
       if (!validarCPF(document.getElementById('regCPF').value)) {
           document.getElementById('registerError').textContent = 'CPF inválido';
           return;
       }
       firebase.auth().createUserWithEmailAndPassword(user.email, user.password)
           .then(userCredential => {
               return db.collection('usuarios').doc(user.username).set(user);
           })
           .then(() => {
               document.getElementById('registerSuccess').textContent = 'Usuário cadastrado com sucesso';
               setTimeout(() => showSection('manageUsers'), 1000);
           })
           .catch(error => {
               document.getElementById('registerError').textContent = error.message;
           });
   }

   // Login com Firebase
   function login() {
       const username = document.getElementById('loginUsername').value.trim();
       const password = document.getElementById('loginPassword').value;
       firebase.auth().signInWithEmailAndPassword(`${username}@admz.app`, password)
           .then(userCredential => {
               return db.collection('usuarios').doc(username).get();
           })
           .then(doc => {
               if (doc.exists) {
                   currentUser = doc.data();
                   document.getElementById('adminName').textContent = currentUser.fullName;
                   document.getElementById('trackingUsername').textContent = currentUser.fullName;
                   showSection(currentUser.isAdmin ? 'adminDashboard' : 'timeTracking');
               } else {
                   document.getElementById('loginError').textContent = 'Usuário não encontrado';
               }
           })
           .catch(error => {
               document.getElementById('loginError').textContent = 'Usuário ou senha inválidos';
           });
   }
   ```
