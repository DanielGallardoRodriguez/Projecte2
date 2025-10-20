# Recuperació d'accés i protecció del sistema operatiu
## 1. Configuració de la màquina virtual

Per iniciar el procés de recuperació i protecció del sistema, es crea una màquina virtual amb les següents especificacions:

- Memòria RAM: 8 GB  
- Processadors: 2  
- Disc dur: es reutilitza un fitxer de disc virtual existent

Es fa servir una imatge ISO ja preparada. A la configuració del disc dur, es selecciona:


Amb això, la màquina queda llesta per arrencar.

---

## 2. Restabliment de la contrasenya des del GRUB

Quan s’inicia la màquina, es detecta que no es pot accedir a l’usuari principal.  
Per restablir la contrasenya, s’utilitza el GRUB per iniciar el sistema amb permisos de root.

### Passos:

1. Reiniciar la màquina i prémer la tecla `Esc` fins que aparegui el menú del GRUB.
2. Editar l’entrada d’arrencada i escriure:

<img width="563" height="28" alt="image" src="https://github.com/user-attachments/assets/dd6ae2a3-da01-4c2c-a34e-f4ab36f4b0f4" />

linux /boot/vmlinuz-6.8.0-52-generic root=/dev/sda3 rw init=/bin/bash
initrd /boot/initrd.img-6.8.0-52-generic
boot

3. Un cop accedit al sistema com a root, executar:


passwd miquel

4. Assignar una nova contrasenya i reiniciar la màquina.

---

## 3. Protecció del GRUB amb contrasenya

Per evitar modificacions no autoritzades del GRUB, es configura una contrasenya d’administrador.

### Procediment:

1. Generar un hash encriptat amb:


grub-mkpasswd-pbkdf2

<img width="318" height="60" alt="image" src="https://github.com/user-attachments/assets/873a9f2c-2357-4dff-a772-62e13396c4bb" />


2. Copiar el hash generat i editar el fitxer:


sudo nano /etc/grub.d/40_custom

3. Afegir les línies següents:


set superusers="root"
password_pbkdf2 root <hash_encriptat>

4. Guardar els canvis i actualitzar el GRUB:


sudo update-grub

---

## 4. Protecció avançada del GRUB

Per permetre que el sistema s’iniciï automàticament però protegir les opcions d’edició:

1. Obrir el fitxer que genera les entrades del GRUB:


sudo nano /etc/grub.d/10_linux

2. Buscar les línies amb `menuentry` i afegir el paràmetre:


--unrestricted

<img width="575" height="259" alt="image" src="https://github.com/user-attachments/assets/89ba7825-a55c-4b1c-a065-72c5db6be92f" />


just després de `${CLASS}`.

3. Guardar els canvis i actualitzar el GRUB:


sudo update-grub

4. Reiniciar la màquina per verificar que el sistema arrenca correctament i que les opcions avançades queden protegides.

---

## 5. Resultat final

Amb aquesta configuració:

- El sistema s’inicia automàticament sense demanar contrasenya.
- Les opcions d’edició del GRUB queden protegides amb autenticació.
- Es garanteix una protecció eficaç sense comprometre la comoditat de l’usuari.
