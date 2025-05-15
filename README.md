 <!DOCTYPE html>
    <html lang="zh-CN">
    
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>首页注册表单</title>
      <style>
        .container {
          max-width: 600px;
          margin: 0 auto;
          background-color: #fff;
          padding: 30px;
          border-radius: 8px;
          box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        
        h1 {
          text-align: center;
          color: #2c3e50;
          margin-bottom: 30px;
        }
        
        .form-group {
          margin-bottom: 20px;
          position: relative;
        }
        
        label {
          display: block;
          margin-bottom: 5px;
          font-weight: bold;
        }
        
        input[type="text"],
        input[type="email"],
        input[type="password"],
        select,
        textarea {
          width: 100%;
          padding: 10px;
          border: 1px solid #ddd;
          border-radius: 4px;
          font-size: 16px;
          box-sizing: border-box;
        }
        
        textarea {
          height: 100px;
          resize: vertical;
        }
        
        .error {
          color: #e74c3c;
          font-size: 14px;
          margin-top: 5px;
          display: none;
          position: absolute;
          left: 0;
        }
        
        button {
          background-color: #3498db;
          color: white;
          border: none;
          padding: 12px 20px;
          font-size: 16px;
          border-radius: 4px;
          cursor: pointer;
          width: 100%;
          transition: background-color 0.3s;
        }
        
        button:hover {
          background-color: #2980b9;
        }
        
        .success-message {
          background-color: #2ecc71;
          color: white;
          padding: 15px;
          border-radius: 4px;
          margin-bottom: 20px;
          display: none;
        }
        
        .user-list {
          margin-top: 30px;
        }
        
        .user-card {
          background-color: #f9f9f9;
          padding: 15px;
          border-radius: 4px;
          margin-bottom: 10px;
          border-left: 4px solid #3498db;
        }
        
        .user-card h3 {
          margin-top: 0;
          color: #2c3e50;
        }
        
        .user-card p {
          margin: 5px 0;
        }
        
        .required:after {
          content: " *";
          color: #e74c3c;
        }
        
        .input-error {
          border-color: #e74c3c !important;
        }
      </style>
    </head>
    
    <body>
      <div class="container">
        <div id="successMessage" class="success-message">
          注册成功！您的信息已保存。
        </div>
        
        <form id="registrationForm">
          <div class="form-group">
            <label for="username" class="required">用户名</label>
            <input type="text" id="username" name="username" required>
            <div id="usernameError" class="error">用户名必须为4-16个字符，只能包含字母和数字</div>
          </div>
          
          <div class="form-group">
            <label for="email" class="required">电子邮箱</label>
            <input type="email" id="email" name="email" required>
            <div id="emailError" class="error">请输入有效的电子邮箱地址</div>
          </div>
          
          <div class="form-group">
            <label for="password" class="required">密码</label>
            <input type="password" id="password" name="password" required>
            <div id="passwordError" class="error">密码必须至少8个字符，包含字母和数字</div>
          </div>
          
          <div class="form-group">
            <label for="confirmPassword" class="required">确认密码</label>
            <input type="password" id="confirmPassword" name="confirmPassword" required>
            <div id="confirmPasswordError" class="error">两次输入的密码不一致</div>
          </div>
          
          <div class="form-group">
            <label for="gender">性别</label>
            <select id="gender" name="gender">
              <option value="">请选择</option>
              <option value="male">男</option>
              <option value="female">女</option>
              <option value="other">其他</option>
            </select>
          </div>
          
          <div class="form-group">
            <label for="birthdate">出生日期</label>
            <input type="date" id="birthdate" name="birthdate">
          </div>
          
          <div class="form-group">
            <label for="interests">兴趣爱好（可多选）</label>
            <div id="interests">
              <label><input type="checkbox" name="interests" value="sports"> 运动</label>
              <label><input type="checkbox" name="interests" value="music"> 音乐</label>
              <label><input type="checkbox" name="interests" value="reading"> 阅读</label>
              <label><input type="checkbox" name="interests" value="travel"> 旅行</label>
              <label><input type="checkbox" name="interests" value="cooking"> 烹饪</label>
            </div>
          </div>
          
          <div class="form-group">
            <label for="bio">个人简介</label>
            <textarea id="bio" name="bio"></textarea>
          </div>
          
          <div class="form-group">
            <button type="submit" id="submitBtn">注册</button>
          </div>
        </form>
        
        <div id="userList" class="user-list">
          <h2>已注册用户</h2>
          <div id="usersContainer"></div>
        </div>
      </div>
    
      <script>
        document.addEventListener('DOMContentLoaded', function() {
          const form = document.getElementById('registrationForm');
          const successMessage = document.getElementById('successMessage');
          const usersContainer = document.getElementById('usersContainer');
          
          // 表单元素
          const usernameInput = document.getElementById('username');
          const emailInput = document.getElementById('email');
          const passwordInput = document.getElementById('password');
          const confirmPasswordInput = document.getElementById('confirmPassword');
          
          // 错误提示元素
          const usernameError = document.getElementById('usernameError');
          const emailError = document.getElementById('emailError');
          const passwordError = document.getElementById('passwordError');
          const confirmPasswordError = document.getElementById('confirmPasswordError');
          
          // 从本地存储加载已注册用户
          loadRegisteredUsers();
          
          // 实时验证用户名唯一性
          usernameInput.addEventListener('blur', function() {
            const username = this.value;
            const users = JSON.parse(localStorage.getItem('registeredUsers') || '[]');
            
            if (username.length >= 4) {
              const isUnique = !users.some(user => user.username === username);
              
              if (!isUnique) {
                usernameError.textContent = '该用户名已被使用';
                showError(usernameInput, usernameError);
              } else {
                hideError(usernameInput, usernameError);
              }
            }
          });
          
          // 实时验证用户名格式
          usernameInput.addEventListener('input', function() {
            const username = this.value;
            const usernameRegex = /^[a-zA-Z0-9]{4,16}$/;
            
            if (!usernameRegex.test(username) && username.length > 0) {
              usernameError.textContent = '用户名必须为4-16个字符，只能包含字母和数字';
              showError(usernameInput, usernameError);
            } else {
              hideError(usernameInput, usernameError);
            }
          });
          
          // 实时验证密码格式
          passwordInput.addEventListener('input', function() {
            const password = this.value;
            const passwordRegex = /^(?=.*[a-zA-Z])(?=.*\d).{8,}$/;
            
            if (!passwordRegex.test(password) && password.length > 0) {
              passwordError.textContent = '密码必须至少8个字符，包含字母和数字';
              showError(passwordInput, passwordError);
            } else {
              hideError(passwordInput, passwordError);
            }
          });
          
          // 实时验证密码匹配
          confirmPasswordInput.addEventListener('input', function() {
            const password = passwordInput.value;
            const confirmPassword = this.value;
            
            if (password !== confirmPassword && confirmPassword.length > 0) {
              confirmPasswordError.textContent = '两次输入的密码不一致';
              showError(confirmPasswordInput, confirmPasswordError);
            } else {
              hideError(confirmPasswordInput, confirmPasswordError);
            }
          });
          
          // 表单提交事件
          form.addEventListener('submit', function(e) {
            e.preventDefault();
            
            if (validateForm()) {
              // 收集表单数据
              const userData = {
                username: usernameInput.value,
                email: emailInput.value,
                password: passwordInput.value, // 实际应用中不应存储明文密码
                gender: document.getElementById('gender').value,
                birthdate: document.getElementById('birthdate').value,
                interests: Array.from(document.querySelectorAll('input[name="interests"]:checked'))
                                .map(checkbox => checkbox.value),
                bio: document.getElementById('bio').value,
                timestamp: new Date().toISOString()
              };
              
              // 保存到本地存储
              saveUser(userData);
              
              // 显示成功消息
              successMessage.style.display = 'block';
              
              // 3秒后隐藏成功消息
              setTimeout(() => {
                successMessage.style.display = 'none';
              }, 3000);
              
              // 重置表单
              form.reset();
              
              // 刷新用户列表
              loadRegisteredUsers();
            }
          });
          
          // 显示错误提示
          function showError(inputElement, errorElement) {
            inputElement.classList.add('input-error');
            errorElement.style.display = 'block';
          }
          
          // 隐藏错误提示
          function hideError(inputElement, errorElement) {
            inputElement.classList.remove('input-error');
            errorElement.style.display = 'none';
          }
          
          // 表单验证
          function validateForm() {
            let isValid = true;
            
            // 验证用户名
            const username = usernameInput.value;
            const usernameRegex = /^[a-zA-Z0-9]{4,16}$/;
            if (!usernameRegex.test(username)) {
              usernameError.textContent = '用户名必须为4-16个字符，只能包含字母和数字';
              showError(usernameInput, usernameError);
              isValid = false;
            }
            
            // 验证邮箱
            const email = emailInput.value;
            const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
            if (!emailRegex.test(email)) {
              showError(emailInput, emailError);
              isValid = false;
            } else {
              hideError(emailInput, emailError);
            }
            
            // 验证密码
            const password = passwordInput.value;
            const passwordRegex = /^(?=.*[a-zA-Z])(?=.*\d).{8,}$/;
            if (!passwordRegex.test(password)) {
              passwordError.textContent = '密码必须至少8个字符，包含字母和数字';
              showError(passwordInput, passwordError);
              isValid = false;
            }
            
            // 验证确认密码
            const confirmPassword = confirmPasswordInput.value;
            if (password !== confirmPassword) {
              confirmPasswordError.textContent = '两次输入的密码不一致';
              showError(confirmPasswordInput, confirmPasswordError);
              isValid = false;
            }
            
            return isValid;
          }
          
          // 保存用户到本地存储
          function saveUser(userData) {
            let users = JSON.parse(localStorage.getItem('registeredUsers') || '[]');
            users.push(userData);
            localStorage.setItem('registeredUsers', JSON.stringify(users));
          }
          
          // 从本地存储加载用户并显示
          function loadRegisteredUsers() {
            const users = JSON.parse(localStorage.getItem('registeredUsers') || '[]');
            
            if (users.length === 0) {
              usersContainer.innerHTML = '<p>暂无注册用户</p>';
              return;
            }
            
            usersContainer.innerHTML = '<h2>已注册用户</h2>';
            
            users.forEach(user => {
              const userCard = document.createElement('div');
              userCard.className = 'user-card';
              
              const interests = user.interests.length > 0 ? 
                `兴趣爱好: ${user.interests.join(', ')}` : '无兴趣爱好';
              
              userCard.innerHTML = `
                <h3>${user.username}</h3>
                <p><strong>邮箱:</strong> ${user.email}</p>
                <p><strong>性别:</strong> ${user.gender || '未选择'}</p>
                <p><strong>出生日期:</strong> ${user.birthdate || '未提供'}</p>
                <p>${interests}</p>
                <p><strong>个人简介:</strong> ${user.bio || '无'}</p>
                <p><small>注册时间: ${new Date(user.timestamp).toLocaleString()}</small></p>
              `;
              
              usersContainer.appendChild(userCard);
            });
          }
        });
      </script>
    </body>
    </html>
