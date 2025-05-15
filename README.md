<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>注册页面（透明美化版）</title>
  <style>
    body {
      background: url('https://i.postimg.cc/4351xv7f/cxstudy-1747312448116.jpg') no-repeat center center fixed;
      background-size: cover;
      min-height: 100vh;
      margin: 0;
      padding: 0;
    }
    .register-box {
      width: 420px; /* 更宽 */
      margin: 80px auto;
      padding: 36px 36px 24px 36px;
      background: rgba(255,255,255,0.4);
      border-radius: 16px;
      box-shadow: 0 4px 24px rgba(0,0,0,0.18);
      backdrop-filter: blur(8px);
    }
    
    .register-box h2 {
      text-align: center;
      font-size: 2rem;
      font-weight: bold;
      margin-bottom: 24px; /* 标题和下面间隔大一点 */
      letter-spacing: 2px;
    }
    
    .input-group {
      margin-bottom: 19px; /* 间隔更大 */
    }
    
    .input-group label {
      display: block;
      margin-bottom: 8px;
      font-size: 1rem;
      color: #333;
      font-weight: 500;
    }
    
    .input-group input {
      display: block;
      width: 100%;
      padding: 10px 12px;
      border-radius: 8px;
      border: 1px solid #ccc;
      font-size: 1rem;
      box-sizing: border-box;
    }
    
    button {
      width: 100%;
      padding: 12px;
      border: none;
      border-radius: 24px;
      background: linear-gradient(90deg, #a1c4fd 0%, #c2e9fb 100%);
      color: #fff;
      font-size: 18px;
      font-weight: bold;
      cursor: pointer;
      margin-top: 8px;
      box-shadow: 0 4px 12px rgba(161,196,253,0.3), 0 2px 4px rgba(0,0,0,0.08);
      transition: transform 0.1s, box-shadow 0.1s;
    }
    button:hover {
      background: linear-gradient(90deg, #fbc2eb 0%, #a6c1ee 100%);
      transform: translateY(-2px) scale(1.04);
      box-shadow: 0 8px 24px rgba(161,196,253,0.4), 0 4px 8px rgba(0,0,0,0.10);
    }
  </style>
</head>
<body>
  <div class="register-box">
    <h2>用户注册</h2>
    <div id="form"></div>
    <button onclick="register()">注册</button>
    <div id="result"></div>
  </div>
  <script>
    // 正则表达式提前编译
    const emailReg = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    const phoneReg = /^[0-9]{11}$/;

    // 动态生成输入框
    const fields = [
      { id: 'username', label: '用户名', type: 'text' },
      { id: 'password', label: '密码', type: 'password' },
      { id: 'confirm', label: '确认密码', type: 'password' },
      { id: 'email', label: '邮箱', type: 'text' },
      { id: 'phone', label: '手机号', type: 'text' }
    ];
    const formDiv = document.getElementById('form');
    fields.forEach(f => {
      const group = document.createElement('div');
      group.className = 'input-group';
      group.innerHTML = `<label>${f.label}：</label>
        <input id="${f.id}" type="${f.type}">
        <span class="error" id="${f.id}Err"></span>`;
      formDiv.appendChild(group);
    });
    // 密码强度显示
    const strengthDiv = document.createElement('div');
    strengthDiv.id = 'strength';
    document.getElementById('password').after(strengthDiv);

    // 校验函数
    function validateUsername(u) {
      if (!u) return '用户名不能为空';
      if (/[^a-zA-Z0-9]/.test(u)) return '不能有符号';
      if (u.length < 4 || u.length > 16) return '长度4-16位';
      // 检查用户名是否已存在
      const users = JSON.parse(localStorage.getItem('users') || '[]');
      if (users.some(user => user.username === u)) return '用户名已存在';
      return '';
    }
    function validatePassword(p) {
      if (!p) return '密码不能为空';
      if (p.length < 8) return '至少8位';
      if (!/[a-zA-Z]/.test(p) || !/[0-9]/.test(p)) return '需字母和数字';
      return '';
    }
    function validateConfirmPassword(p, c) {
      if (!c) return '请确认密码';
      if (p !== c) return '两次密码不一致';
      return '';
    }
    function validateEmail(e) {
      if (!e) return '邮箱不能为空';
      if (!emailReg.test(e)) return '邮箱格式错误';
      return '';
    }
    function validatePhone(p) {
      if (!p) return '手机号不能为空';
      if (!phoneReg.test(p)) return '必须11位数字';
      return '';
    }

    // 密码强度可视化
    document.getElementById('password').addEventListener('input', function() {
      const val = this.value;
      const strength = getStrength(val);
      const strengthDiv = document.getElementById('strength');
      if (!val) {
        strengthDiv.textContent = '';
        return;
      }
      if (strength === '强') {
        strengthDiv.textContent = '密码强度：强';
        strengthDiv.className = 'strong';
      } else if (strength === '中') {
        strengthDiv.textContent = '密码强度：中';
        strengthDiv.className = 'medium';
      } else {
        strengthDiv.textContent = '密码强度：弱';
        strengthDiv.className = 'weak';
      }
    });

    function getStrength(pwd) {
      let score = 0;
      if (pwd.length >= 8) score++;
      if (/[A-Z]/.test(pwd)) score++;
      if (/[a-z]/.test(pwd)) score++;
      if (/[0-9]/.test(pwd)) score++;
      if (/[^a-zA-Z0-9]/.test(pwd)) score++;
      if (score >= 4) return '强';
      if (score >= 3) return '中';
      return '弱';
    }

    // 注册并存储
    function register() {
      let ok = true;
      const values = {};
      // 校验
      const username = document.getElementById('username').value;
      const password = document.getElementById('password').value;
      const confirm = document.getElementById('confirm').value;
      const email = document.getElementById('email').value;
      const phone = document.getElementById('phone').value;

      // 单独校验
      const usernameErr = validateUsername(username);
      document.getElementById('usernameErr').textContent = usernameErr;
      if (usernameErr) ok = false;

      const passwordErr = validatePassword(password);
      document.getElementById('passwordErr').textContent = passwordErr;
      if (passwordErr) ok = false;

      const confirmErr = validateConfirmPassword(password, confirm);
      document.getElementById('confirmErr').textContent = confirmErr;
      if (confirmErr) ok = false;

      const emailErr = validateEmail(email);
      document.getElementById('emailErr').textContent = emailErr;
      if (emailErr) ok = false;

      const phoneErr = validatePhone(phone);
      document.getElementById('phoneErr').textContent = phoneErr;
      if (phoneErr) ok = false;

      values.username = username;
      values.password = password;
      values.email = email;
      values.phone = phone;

      if (!ok) return;

      // 存储到 users 数组
      let users = JSON.parse(localStorage.getItem('users') || '[]');
      users.push(values);
      localStorage.setItem('users', JSON.stringify(users));
      document.getElementById('result').textContent = '注册成功！';
    }

    // 页面加载时自动填充（仅填充最后一个用户）
    window.onload = function() {
      const users = JSON.parse(localStorage.getItem('users') || '[]');
      if (users.length > 0) {
        const obj = users[users.length - 1];
        fields.forEach(f => {
          document.getElementById(f.id).value = obj[f.id] || '';
        });
        document.getElementById('result').textContent = '已自动填充上次注册信息';
      }
    }
  </script>
</body>
</html>
