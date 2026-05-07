# Wad-Pratical

1: Install Angular CLI 
```
npm install -g @angular/cli
```
2:Create Project 
```
ng new user-auth-app
```
```
cd user-auth-app
```
```
ng serve
```
Generate Components & Service
```
ng g c components/register
```
```
ng g c components/login
```
```
ng g c components/profile
```
```
ng g s services/auth
```

3: AUTH SERVICE -->
📄 src/app/services/auth.ts
```
import { Injectable } from '@angular/core';

@Injectable({
 providedIn: 'root'
})
export class AuthService {

 register(user: any) {
   localStorage.setItem('user', JSON.stringify(user));
 }

 login(email: string, password: string): boolean {
   const user = JSON.parse(localStorage.getItem('user') || '{}');

   if (user.email === email && user.password === password) {
     localStorage.setItem('loggedIn', 'true');
     return true;
   }
   return false;
 }

 getUser() {
   return JSON.parse(localStorage.getItem('user') || '{}');
 }	

 isLoggedIn() {
   return localStorage.getItem('loggedIn') === 'true';
 }

 logout() {
   localStorage.removeItem('loggedIn');
 }
}
```
4: ROUTES SETUP -->
📄 app.routes.ts
```
import { Routes } from '@angular/router';
import { Login } from './components/login/login';
import { Register } from './components/register/register';
import { Profile } from './components/profile/profile';

export const routes: Routes = [
 { path: '', component: Login },
 { path: 'login', component: Login },
 { path: 'register', component: Register },
 { path: 'profile', component: Profile }
];
```

5:REGISTER COMPONENT -->
📄 register.ts
```
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { RouterModule } from '@angular/router';
import { AuthService } from '../../services/auth';
import { Router } from '@angular/router';

@Component({
 selector: 'app-register',
 standalone: true,
 imports: [FormsModule, RouterModule],
 templateUrl: './register.html',
 styleUrl: './register.css'
})
export class Register {

 user = {
   name: '',
   email: '',
   password: ''
 };

 constructor(private auth: AuthService, private router: Router) {}

 registerUser() {
   this.auth.register(this.user);
   alert('Registered Successfully');
   this.router.navigate(['/login']);
 }
}
```
register.html
```
<h2>Register</h2>

<form (ngSubmit)="registerUser()">
 <input type="text" [(ngModel)]="user.name" name="name" placeholder="Name" required><br>
 <input type="email" [(ngModel)]="user.email" name="email" placeholder="Email" required><br>
 <input type="password" [(ngModel)]="user.password" name="password" placeholder="Password" required><br>

 <button type="submit">Register</button>
</form>

<a routerLink="/login">Go to Login</a>
```
6: LOGIN COMPONENT -->
📄 login.ts
```
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { AuthService } from '../../services/auth';
import { RouterModule } from '@angular/router'; 
import { Router } from '@angular/router';

@Component({
 selector: 'app-login',
 standalone: true,
 imports: [FormsModule, RouterModule],
 templateUrl: './login.html',
 styleUrl: './login.css'
})
export class Login {

 email = '';
 password = '';

 constructor(private auth: AuthService, private router: Router) {}

 loginUser() {
   const success = this.auth.login(this.email, this.password);

   if (success) {
     alert('Login Successful');
     this.router.navigate(['/profile']);
   } else {
     alert('Invalid Credentials');
   }
 }
}
```
login.html
```
<h2>Login</h2>

<form (ngSubmit)="loginUser()">
 <input type="email" [(ngModel)]="email" name="email" placeholder="Email" required><br>
 <input type="password" [(ngModel)]="password" name="password" placeholder="Password" required><br>

 <button type="submit">Login</button>
</form>

<a routerLink="/register">Go to Register</a>
```
7: PROFILE COMPONENT --> 
📄 profile.ts
```
import { Component, OnInit } from '@angular/core';
import { AuthService } from '../../services/auth';
import { Router } from '@angular/router';

@Component({
 selector: 'app-profile',
 standalone: true,
 templateUrl: './profile.html',
 styleUrl: './profile.css'
})
export class Profile implements OnInit {

 user: any = {};

 constructor(private auth: AuthService, private router: Router) {}

 ngOnInit() {
   if (!this.auth.isLoggedIn()) {
     this.router.navigate(['/login']);
   }
   this.user = this.auth.getUser();
 }

 logout() {
   this.auth.logout();
   this.router.navigate(['/login']);
 }
}
```

profile.html
```
<h2>Profile</h2>

<p><b>Name:</b> {{ user.name }}</p>
<p><b>Email:</b> {{ user.email }}</p>

<button (click)="logout()">Logout</button>
```
app.ts
```
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';

@Component({
 selector: 'app-root',
 standalone: true,
 imports: [RouterOutlet],
 templateUrl: './app.html',
 styleUrl: './app.css'
})
export class App {
}
```
app.html
```
<router-outlet></router-outlet>
```
