
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Sistema Completo</title>
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
<script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-firestore-compat.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<style>
.section { display: none; margin-top: 20px; }
.active { display: block; }
</style>
</head>
<body>
<div class="container">

<!-- LOGIN -->
<div id="login" class="section">
    <h2>Login</h2>
    <input id="loginUsername" class="form-control mb-2" placeholder="Usuário">
    <input id="loginPassword" class="form-control mb-2" placeholder="Senha" type="password">
    <button class="btn btn-primary w-100 mb-2" onclick="login()">Entrar</button>
    <div id="loginError" class="text-danger"></div>
</div>

<!-- ADMIN -->
<div id="manageUsers" class="section">
    <h2>Gerenciar Usuários</h2>
    <select id="userSelect" class="form-select mb-2" onchange="viewUserProfile()">
        <option value="">Selecione um usuário</option>
    </select>
    <button class="btn btn-secondary mb-2" onclick="showSection('registerUser')">Cadastrar Usuário</button>
    <button class="btn btn-secondary mb-2" onclick="showSection('registerRecord')">Adicionar Registro</button>
    <button class="btn btn-secondary mb-2" onclick="logout()">Sair</button>

    <h3>Registros</h3>
    <table class="table table-bordered" id="recordsTable">
        <thead>
            <tr>
                <th>Usuário</th>
                <th>Data</th>
                <th>Descrição</th>
            </tr>
        </thead>
        <tbody></tbody>
    </table>
    <button class="btn btn-success mb-2" onclick="exportPDF()">Exportar PDF</button>
    <button class="btn btn-success mb-2" onclick="exportCSV()">Exportar CSV</button>
</div>

<!-- REGISTER USER -->
<div id="registerUser" class="section">
    <h2>Cadastrar Usuário</h2>
    <input id="regUsername" class="form-control mb-2" placeholder="Usuário">
    <input id="regFullName" class="form-control mb-2" placeholder="Nome completo">
    <input id="regCPF" class="form-control mb-2" placeholder="CPF">
    <input id="regBirthDate" class="form-control mb-2" type="date">
    <input id="regAddress" class="form-control mb-2" placeholder="Endereço">
    <input id="regAdmissionDate" class="form-control mb-2" type="date">
    <input id="regPosition" class="form-control mb-2" placeholder="Cargo">
    <input id="regPIS" class="form-control mb-2" placeholder="PIS">
    <input id="regCTPS" class="form-control mb-2" placeholder="CTPS">
    <input id="regPassword" class="form-control mb-2" placeholder="Senha" type="password">
    <div class="form-check mb-2">
        <input id="regIsAdmin" class="form-check-input" type="checkbox">
        <label class="form-check-label">Administrador</label>
    </div>
    <button class="btn btn-primary w-100 mb-2" onclick="registerUser()">Cadastrar</button>
    <button class="btn btn-secondary w-100 mb-2" onclick="showSection('manageUsers')">Voltar</button>
    <div id="registerError" class="text-danger"></div>
    <div id="registerSuccess" class="text-success"></div>
</div>

<!-- USER PROFILE -->
<div id="userProfile" class="section">
    <h2>Perfil do Usuário</h2>
    <input id="profileUsername" class="form-control mb-2" placeholder="Usuário" readonly>
    <input id="profileFullName" class="form-control mb-2" placeholder="Nome completo">
    <input id="profileCPF" class="form-control mb-2" placeholder="CPF">
    <input id="profileBirthDate" class="form-control mb-2" type="date">
    <input id="profileAddress" class="form-control mb-2" placeholder="Endereço">
    <input id="profileAdmissionDate" class="form-control mb-2" type="date">
    <input id="profilePosition" class="form-control mb-2" placeholder="Cargo">
    <input id="profilePIS" class="form-control mb-2" placeholder="PIS">
    <input id="profileCTPS" class="form-control mb-2" placeholder="CTPS">
    <input id="profilePassword" class="form-control mb-2" placeholder="Senha" type="password">
    <div class="form-check mb-2">
        <input id="profileIsAdmin" class="form-check-input" type="checkbox">
        <label class="form-check-label">Administrador</label>
    </div>
    <button class="btn btn-primary w-100 mb-2" onclick="saveUserProfile()">Salvar</button>
    <button class="btn btn-danger w-100 mb-2" onclick="deleteUser()">Excluir Usuário</button>
    <button class="btn btn-secondary w-100 mb-2" onclick="showSection('manageUsers')">Voltar</button>
</div>

<!-- REGISTER RECORD -->
<div id="registerRecord" class="section">
    <h2>Adicionar Registro</h2>
    <input id="recordUsername" class="form-control mb-2" placeholder="Usuário">
    <input id="recordDate" class="form-control mb-2" type="date">
    <input id="recordDesc" class="form-control mb-2" placeholder="Descrição">
    <button class="btn btn-primary w-100 mb-2" onclick="addRecord()">Adicionar</button>
    <button class="btn btn-secondary w-100 mb-2" onclick="showSection('manageUsers')">Voltar</button>
</div>

</div>

<script>
// Firebase config
const firebaseConfig = {
    apiKey: "AIzaSyCwHEYAAWzuArnjHKyBB2HQornlGXgXcwc",
    authDomain: "admz-4cc94.firebaseapp.com",
    projectId: "admz-4cc94",
    storageBucket: "admz-4cc94.appspot.com",
    messagingSenderId: "1058002382869",
    appId: "1:1058002382869:web:01e295c26aab0c662b1e1c",
    measurementId: "G-WFJ4D9JFGC"
};

// Inicializa Firebase
const app = firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();
const db = firebase.firestore();
let currentUser = null;

function showSection(id){
    document.querySelectorAll(".section").forEach(s => s.classList.remove("active"));
    document.getElementById(id)?.classList.add("active");
    if(id === 'manageUsers') loadUsers();
    if(id === 'manageUsers') loadRecords();
}

// LOGIN
async function login(){
    const username = document.getElementById("loginUsername").value;
    const password = document.getElementById("loginPassword").value;
    try{
        await auth.signInWithEmailAndPassword(username+"@admz.app", password);
        currentUser = username;
        showSection('manageUsers');
    } catch(e){
        document.getElementById("loginError").textContent = e.message;
    }
}

async function logout(){
    await auth.signOut();
    currentUser = null;
    showSection('login');
}

// CPF Validation
function validarCPF(cpf){
    cpf = cpf.replace(/[^\d]+/g,'');
    if(cpf.length !== 11) return false;
    let sum=0,r;
    for(let i=1;i<=9;i++) sum+=parseInt(cpf[i-1])*(11-i);
    r=(sum*10)%11;
    if(r===10||r===11) r=0;
    if(r!==parseInt(cpf[9])) return false;
    sum=0;
    for(let i=1;i<=10;i++) sum+=parseInt(cpf[i-1])*(12-i);
    r=(sum*10)%11;
    if(r===10||r===11) r=0;
    if(r!==parseInt(cpf[10])) return false;
    return true;
}

// USER MANAGEMENT
async function registerUser(){
    const u = {
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
        isAdmin: document.getElementById('regIsAdmin').checked
    };
    if(!u.username || !u.fullName || !u.password){ document.getElementById("registerError").textContent="Preencha os campos obrigatórios"; return;}
    if(u.cpf && !validarCPF(u.cpf)){ document.getElementById("registerError").textContent="CPF inválido"; return;}
    try{
        const userDoc = await db.collection("usuarios").doc(u.username).get();
        if(userDoc.exists){ document.getElementById("registerError").textContent="Usuário já existe"; return; }
        await auth.createUserWithEmailAndPassword(u.username+"@admz.app", u.password);
        await db.collection("usuarios").doc(u.username).set(u);
        document.getElementById("registerSuccess").textContent="Usuário cadastrado!";
        setTimeout(()=>showSection('manageUsers'),1000);
    } catch(e){ document.getElementById("registerError").textContent=e.message;}
}

async function loadUsers(){
    const select=document.getElementById('userSelect');
    select.innerHTML='<option value="">Selecione um usuário</option>';
    const snapshot=await db.collection("usuarios").get();
    snapshot.forEach(doc=>{
        const u=doc.data();
        const opt=document.createElement("option");
        opt.value=u.username;
        opt.textContent=u.fullName;
        select.appendChild(opt);
    });
}

async function viewUserProfile(){
    const username=document.getElementById('userSelect').value;
    if(!username) return;
    const doc=await db.collection("usuarios").doc(username).get();
    if(doc.exists){
        const u=doc.data();
        document.getElementById('profileUsername').value=u.username;
        document.getElementById('profileFullName').value=u.fullName;
        document.getElementById('profileCPF').value=u.cpf;
        document.getElementById('profileBirthDate').value=u.birthDate;
        document.getElementById('profileAddress').value=u.address;
        document.getElementById('profileAdmissionDate').value=u.admissionDate;
        document.getElementById('profilePosition').value=u.position;
        document.getElementById('profilePIS').value=u.pis;
        document.getElementById('profileCTPS').value=u.ctps;
        document.getElementById('profilePassword').value=u.password;
        document.getElementById('profileIsAdmin').checked=u.isAdmin;
        showSection('userProfile');
    }
}

async function saveUserProfile(){
    const username=document.getElementById('profileUsername').value;
    if(document.getElementById('profileCPF').value && !validarCPF(document.getElementById('profileCPF').value)){ alert("CPF inválido"); return; }
    await db.collection("usuarios").doc(username).update({
        fullName: document.getElementById('profileFullName').value,
        cpf: document.getElementById('profileCPF').value,
        birthDate: document.getElementById('profileBirthDate').value,
        address: document.getElementById('profileAddress').value,
        admissionDate: document.getElementById('profileAdmissionDate').value,
        position: document.getElementById('profilePosition').value,
        pis: document.getElementById('profilePIS').value,
        ctps: document.getElementById('profileCTPS').value,
        password: document.getElementById('profilePassword').value,
        isAdmin: document.getElementById('profileIsAdmin').checked
    });
    alert("Perfil atualizado!");
    showSection('manageUsers');
}

async function deleteUser(){
    const username=document.getElementById('profileUsername').value;
    await db.collection("usuarios").doc(username).delete();
    alert("Usuário excluído!");
    showSection('manageUsers');
}

// RECORD MANAGEMENT
async function addRecord(){
    const r = {
        username: document.getElementById('recordUsername').value,
        date: document.getElementById('recordDate').value,
        desc: document.getElementById('recordDesc').value
    };
    if(!r.username || !r.date || !r.desc){ alert("Preencha todos os campos"); return;}
    await db.collection("registros").add(r);
    alert("Registro adicionado!");
    showSection('manageUsers');
    loadRecords();
}

async function loadRecords(){
    const tbody=document.querySelector("#recordsTable tbody");
    tbody.innerHTML="";
    const snapshot=await db.collection("registros").get();
    snapshot.forEach(doc=>{
        const r=doc.data();
        const tr=document.createElement("tr");
        tr.innerHTML=`<td>${r.username}</td><td>${r.date}</td><td>${r.desc}</td>`;
        tbody.appendChild(tr);
    });
}

// EXPORT PDF
function exportPDF(){
    const { jsPDF } = window.jspdf;
    const doc = new jsPDF();
    let y = 10;
    doc.text("Registros", 10, y);
    y+=10;
    document.querySelectorAll("#recordsTable tbody tr").forEach(tr=>{
        const tds = tr.querySelectorAll("td");
        doc.text(`${tds[0].textContent} | ${tds[1].textContent} | ${tds[2].textContent}`, 10, y);
        y+=10;
    });
    doc.save("registros.pdf");
}

// EXPORT CSV
function exportCSV(){
    let csv="Usuário,Data,Descrição\n";
    document.querySelectorAll("#recordsTable tbody tr").forEach(tr=>{
        const tds = tr.querySelectorAll("td");
        csv+=`${tds[0].textContent},${tds[1].textContent},${tds[2].textContent}\n`;
    });
    const blob=new Blob([csv],{type:"text/csv"});
    const link=document.createElement("a");
    link.href=URL.createObjectURL(blob);
    link.download="registros.csv";
    link.click();
}

window.onload=function(){ showSection('login'); };
</script>
</body>
</html>
