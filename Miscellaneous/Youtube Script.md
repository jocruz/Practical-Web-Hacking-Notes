# Hook Intro:

¿Alguna vez te has preguntado qué sucede detrás de escena cuando olvidas tu contraseña? ¿Y si te dijera que esos códigos en tu correo o notificaciones en tu app no son solo conveniencia, sino defensas contra vulnerabilidades serias? En este video, te guiaré paso a paso a través del laboratorio de PortSwigger 'Password Reset Broken Logic', explicando qué puede pasar si esas funciones están mal implementadas. Al final, entenderás por qué un simple enlace de restablecimiento puede ser la diferencia entre que te hackeen o ser tú quien encuentre la vulnerabilidad. ¡No te lo pierdas, porque esta lección podría cambiar tu forma de ver la ciberseguridad!




---

### **Step 1: Initial Recon / Reconocimiento Inicial**

**What we’re doing:** We’re logging in as Wiener and requesting a password reset link.  
**Qué estamos haciendo:** Vamos a iniciar sesión como Wiener y solicitar un enlace de restablecimiento de contraseña.

**Steps / Pasos:**

1. Log in with the username: `wiener` and password: `peter`.
    - Inicia sesión con el usuario: `wiener` y la contraseña: `peter`.
2. Go to the **"Forgot your password?"** page.
    - Ve a la página de **"¿Olvidaste tu contraseña?"**.
3. Enter the email: `wiener@pw.com` and submit the form.
    - Ingresa el correo electrónico: `wiener@pw.com` y envía el formulario.
4. Open the **email client tab** to view the reset link.
    - Abre la **pestaña del cliente de correo** para ver el enlace de restablecimiento.

---

### **Step 2: Intercept and Analyze Requests / Interceptar y Analizar Solicitudes**

**What we’re doing:** We’ll intercept the password reset request with Burp Suite to analyze how it works.  
**Qué estamos haciendo:** Vamos a interceptar la solicitud de restablecimiento de contraseña con Burp Suite para analizar cómo funciona.

**Steps / Pasos:**

1. Open **Burp Suite** and configure it to intercept browser traffic.
    - Abre **Burp Suite** y configura para interceptar el tráfico del navegador.
2. Intercept the password reset process.
    - Intercepta el proceso de restablecimiento de contraseña.
3. Analyze the `POST` requests, focusing on:
    - Analiza las solicitudes `POST`, enfocándote en:
    - The `temp-forgot-password-token` field.
        - El campo `temp-forgot-password-token`.
    - Hidden fields like `username`, `new-password`, and `new-password-2`.
        - Campos ocultos como `username`, `new-password`, y `new-password-2`.

---

### **Step 3: Test the Vulnerability / Probar la Vulnerabilidad**

**What we’re doing:** We’ll test whether the application properly validates the token.  
**Qué estamos haciendo:** Vamos a probar si la aplicación valida correctamente el token.

**Steps / Pasos:**

1. Use the reset link to change your own password and confirm it works.
    - Usa el enlace de restablecimiento para cambiar tu propia contraseña y confirma que funciona.
2. In **Burp Repeater**, edit the `temp-forgot-password-token` field:
    - En **Burp Repeater**, edita el campo `temp-forgot-password-token`:
    - Delete its value or replace it with arbitrary text.
        - Borra su valor o reemplázalo con texto cualquiera.
3. Observe whether the process still works, indicating the token is not validated properly.
    - Observa si el proceso sigue funcionando, indicando que no se valida correctamente.

---

### **Step 4: Exploit the Vulnerability / Explotar la Vulnerabilidad**

**What we’re doing:** We’ll reset Carlos’ password by modifying the request.  
**Qué estamos haciendo:** Vamos a restablecer la contraseña de Carlos modificando la solicitud.

**Steps / Pasos:**

1. Request another password reset link.
    - Solicita otro enlace de restablecimiento de contraseña.
2. Intercept the `POST /forgot-password` request.
    - Intercepta la solicitud `POST /forgot-password`.
3. Change the `username` parameter to `carlos`.
    - Cambia el parámetro `username` a `carlos`.
4. Set the `new-password` to your desired value (e.g., `password`).
    - Define el campo `new-password` con la contraseña que desees (por ejemplo, `password`).
5. Send the request and confirm success with the `HTTP/2 302 Found` response.
    - Envía la solicitud y confirma el éxito con la respuesta `HTTP/2 302 Found`.

---

### **Step 5: Validate Exploitation / Validar la Explotación**

**What we’re doing:** We’ll log in as Carlos to confirm the lab is solved.  
**Qué estamos haciendo:** Vamos a iniciar sesión como Carlos para confirmar que completamos el laboratorio.

**Steps / Pasos:**

1. Log in as `carlos` with the new password.
    - Inicia sesión como `carlos` con la nueva contraseña.
2. Navigate to the **"My Account"** page to complete the lab.
    - Ve a la página de **"Mi Cuenta"** para completar el laboratorio.

---

This bilingual format ensures you can both understand and read the instructions with ease while recording. 😊