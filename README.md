# PIXELWISE

This is a one-line project description of pixelwise, a full stack project
that teaches students how to deploy a small machine learning product on
a virtual machine.

First thing they learn is how to change things here in this repo. 
Such as this sentence itself.


## Secrets mit SOPS/age

Secrets liegen verschlüsselt in `secrets.enc.ini`. Key ist hier zu Demonstrationszwecken bewusst öffentlich, in der Praxis würde man ihn separat verteilen.

### Voraussetzungen

```sh
# Arch / CachyOS
sudo pacman -S age sops

# Debian / Ubuntu
sudo apt install age sops

# macOS
brew install age sops
```

### key.txt

Liegt im Projekt-Root, also auf gleicher Ebene wie `secrets.enc.ini`. Steht in `.gitignore`, wird nie committet. Inhalt ist genau eine Zeile, der private key:

```sh
cat > key.txt << 'EOF'
AGE-SECRET-KEY-1M4C0JCZ5EJ72J5RRKHPJFP954EY2YUCG4KKFSJMJEXD4N53G3TUS6YSWJS
EOF
```

Public key, der zum Verschlüsseln gebraucht wird:

```
age1aquf4avl0a8ydc40h7uc8gw9u86s6wt5prkgrpcyurvpc45mpp4qr3gr8t
```

### Neues Secret hinzufügen und pushen

```sh
export SOPS_AGE_KEY_FILE=key.txt
sops -d secrets.enc.ini > secrets.ini      # entschlüsseln
nano secrets.ini                           # Zeile hinzufügen/ändern, z.B. NEW_KEY=wert
sops -e --age age1aquf4avl0a8ydc40h7uc8gw9u86s6wt5prkgrpcyurvpc45mpp4qr3gr8t secrets.ini > secrets.enc.ini   # neu verschlüsseln
git add secrets.enc.ini
git commit -m "secrets aktualisiert"
git push
```

`secrets.ini` danach löschen oder einfach lokal liegen lassen, sie wird nicht mitcommittet.

### Pullen und entschlüsseln (Passwörter sehen)

```sh
git pull
export SOPS_AGE_KEY_FILE=key.txt
sops -d secrets.enc.ini > secrets.ini
cat secrets.ini
```

`key.txt` muss dafür schon lokal liegen, einmal wie oben angelegt.