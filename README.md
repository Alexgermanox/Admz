<!DOCTYPE html>
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
        input, select, button { padding: 8px; margin: 5px 0; width: calc(100% - 16px); }
        button { background: #007BFF; color: white; border: none; cursor: pointer; }
        button:hover { background: #0056b3; }
        table { width: 100%; border-collapse: collapse; margin: 20px 0; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
        th { background: #f2f2f2; }
        .error { color: red; }
        .success { color: green; }
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
            <p>Assinatura do funcionário: ____________________</p>
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
            <p>Assinatura do funcionário: ____________________</p>
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

        function showSection(sectionId) {
            document.querySelectorAll('.section').forEach(section => section.classList.remove('active'));
            document.getElementById(sectionId).classList.add('active');
            if (sectionId === 'manageUsers') loadUsers();
            if (sectionId === 'userProfile') loadUserProfile();
            if (sectionId === 'userRecords') loadUserRecords();
            if (sectionId === 'myRecords') loadMyRecords();
            if (sectionId === 'timeTracking') updateCurrentTime();
        }

        function login() {
            const username = document.getElementById('loginUsername').value;
            const password = document.getElementById('loginPassword').value;
            const users = JSON.parse(localStorage.getItem('users') || '[]');
            const user = users.find(u => u.username === username && u.password === password);
            if (user) {
                currentUser = user;
                document.getElementById('adminName').textContent = user.fullName;
                document.getElementById('trackingUsername').textContent = user.fullName;
                showSection(user.isAdmin ? 'adminDashboard' : 'timeTracking');
            } else {
                alert('Usuário ou senha inválidos');
            }
        }

        function logout() {
            currentUser = null;
            showSection('login');
        }

        function registerUser() {
            const user = {
                username: document.getElementById('regUsername').value,
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
            const users = JSON.parse(localStorage.getItem('users') || '[]');
            if (users.some(u => u.username === user.username)) {
                alert('Usuário já existe');
                return;
            }
            users.push(user);
            localStorage.setItem('users', JSON.stringify(users));
            alert('Usuário cadastrado com sucesso');
            showSection('manageUsers');
        }

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

        function showDeleteUserConfirm() {
            showSection('deleteUserConfirm');
        }

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

        function updateCurrentTime() {
            const now = new Date();
            document.getElementById('currentTime').textContent = now.toLocaleString('pt-BR');
            setTimeout(updateCurrentTime, 1000);
        }

        function recordTime(type) {
            const records = JSON.parse(localStorage.getItem('records') || '[]');
            records.push({
                username: currentUser.username,
                date: new Date().toISOString().split('T')[0],
                time: new Date().toLocaleTimeString('pt-BR'),
                type
            });
            localStorage.setItem('records', JSON.stringify(records));
            alert(`${type} registrada com sucesso`);
        }

        function loadUserRecords() {
            const username = document.getElementById('userSelect').value;
            const records = JSON.parse(localStorage.getItem('records') || '[]').filter(r => r.username === username);
            const table = document.getElementById('recordsTable');
            table.innerHTML = '';
            const groupedRecords = groupRecordsByDate(records);
            for (const date in groupedRecords) {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${date}</td>
                    <td>${groupedRecords[date].Entrada || ''}</td>
                    <td>${groupedRecords[date].Pausa || ''}</td>
                    <td>${groupedRecords[date].Retorno || ''}</td>
                    <td>${groupedRecords[date].Saída || ''}</td>
                    <td><button onclick="editRecord('${username}', '${date}', 'Entrada')">Editar</button></td>
                `;
                table.appendChild(row);
            }
            updateReportSummary(records, 'reportSummary');
        }

        function loadMyRecords() {
            const records = JSON.parse(localStorage.getItem('records') || '[]').filter(r => r.username === currentUser.username);
            const table = document.getElementById('myRecordsTable');
            table.innerHTML = '';
            const groupedRecords = groupRecordsByDate(records);
            for (const date in groupedRecords) {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${date}</td>
                    <td>${groupedRecords[date].Entrada || ''}</td>
                    <td>${groupedRecords[date].Pausa || ''}</td>
                    <td>${groupedRecords[date].Retorno || ''}</td>
                    <td>${groupedRecords[date].Saída || ''}</td>
                `;
                table.appendChild(row);
            }
            updateReportSummary(records, 'myReportSummary');
        }

        function groupRecordsByDate(records) {
            const grouped = {};
            records.forEach(r => {
                if (!grouped[r.date]) grouped[r.date] = {};
                grouped[r.date][r.type] = r.time;
            });
            return grouped;
        }

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

        function editRecord(username, date, type) {
            const records = JSON.parse(localStorage.getItem('records') || '[]');
            const record = records.find(r => r.username === username && r.date === date && r.type === type);
            if (record) {
                editingRecord = record;
                document.getElementById('editRecordDateTime').value = `${record.date}T${record.time}`;
                document.getElementById('editRecordType').value = record.type;
                showSection('editRecord');
            }
        }

        function saveRecord() {
            const records = JSON.parse(localStorage.getItem('records') || '[]');
            const index = records.findIndex(r => r === editingRecord);
            if (index !== -1) {
                const dateTime = document.getElementById('editRecordDateTime').value;
                records[index] = {
                    ...editingRecord,
                    date: dateTime.split('T')[0],
                    time: dateTime.split('T')[1],
                    type: document.getElementById('editRecordType').value
                };
                localStorage.setItem('records', JSON.stringify(records));
                alert('Registro atualizado com sucesso');
                showSection('userRecords');
            }
        }

        function cancelEditRecord() {
            showSection('userRecords');
        }

        function showDeleteRecordConfirm() {
            showSection('deleteRecordConfirm');
        }

        function deleteRecord() {
            const records = JSON.parse(localStorage.getItem('records') || '[]');
            const index = records.findIndex(r => r === editingRecord);
            if (index !== -1) {
                records.splice(index, 1);
                localStorage.setItem('records', JSON.stringify(records));
                alert('Registro excluído com sucesso');
                showSection('userRecords');
            }
        }

        function cancelDeleteRecord() {
            showSection('userRecords');
        }

        function showClearRecordsConfirm() {
            showSection('clearRecordsConfirm');
        }

        function clearRecords() {
            let records = JSON.parse(localStorage.getItem('records') || '[]');
            records = records.filter(r => r.username !== currentUser.username);
            localStorage.setItem('records', JSON.stringify(records));
            alert('Registros limpos com sucesso');
            showSection('timeTracking');
        }

        function exportRecordsPDF() {
            const username = document.getElementById('userSelect').value;
            const records = JSON.parse(localStorage.getItem('records') || '[]').filter(r => r.username === username);
            const doc = new jsPDF();
            doc.text(`Registros do Usuário: ${username}`, 10, 10);
            let y = 20;
            const groupedRecords = groupRecordsByDate(records);
            for (const date in groupedRecords) {
                doc.text(`${date}: Entrada: ${groupedRecords[date].Entrada || ''}, Pausa: ${groupedRecords[date].Pausa || ''}, Retorno: ${groupedRecords[date].Retorno || ''}, Saída: ${groupedRecords[date].Saída || ''}`, 10, y);
                y += 10;
            }
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
        }

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
        }

        function exportRecordsCSV() {
            const records = JSON.parse(localStorage.getItem('records') || '[]').filter(r => r.username === currentUser.username);
            const groupedRecords = groupRecordsByDate(records);
            let csv = 'Dia,Entrada,Pausa,Retorno,Saída\n';
            for (const date in groupedRecords) {
                csv += `${date},${groupedRecords[date].Entrada || ''},${groupedRecords[date].Pausa || ''},${groupedRecords[date].Retorno || ''},${groupedRecords[date].Saída || ''}\n`;
            }
            const blob = new Blob([csv], { type: 'text/csv' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `registros_${currentUser.username}.csv`;
            a.click();
            URL.revokeObjectURL(url);
        }

        function removePhoto() {
            const username = document.getElementById('profileUsername').value;
            const users = JSON.parse(localStorage.getItem('users') || '[]');
            const index = users.findIndex(u => u.username === username);
            if (index !== -1) {
                users[index].photo = null;
                localStorage.setItem('users', JSON.stringify(users));
                alert('Foto removida com sucesso');
            }
        }

        // Initialize
        showSection('login');
    </script>
</body>
</html>
