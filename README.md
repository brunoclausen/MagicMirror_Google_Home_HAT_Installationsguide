# 📜 MagicMirror² + Google Home + ReSpeaker HAT: Den Ultimative Installationsguide

## 🛠 Hvad du har brug for

- Raspberry Pi 5 med Raspberry Pi OS (32-bit anbefales)
- microSD-kort (16GB eller større)
- Skærm og HDMI-kabel
- **ReSpeaker 2-Mics HAT eller ReSpeaker 4-Mic Array HAT** (Anbefalet til stemmegenkendelse)
- Google-konto

---

## 🖥 1. Installér MagicMirror²

Åbn terminalen og kør:
```bash
bash -c "$(curl -sL https://raw.githubusercontent.com/MichMich/MagicMirror/master/installers/raspberry.sh)"
npm start
```
Tryk **CTRL + Q** for at afslutte.

---

## 🎤 2. Installér og konfigurer ReSpeaker 2-Mics HAT eller 4-Mic Array

### **2.1 Installer nødvendige pakker**
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install git pulseaudio pulseaudio-utils alsa-utils -y
```

### **2.2 Installer ReSpeaker-drivere**
```bash
git clone https://github.com/respeaker/seeed-voicecard.git
cd seeed-voicecard
sudo ./install.sh
```

### **2.3 Genstart din Raspberry Pi**
```bash
sudo reboot
```

### **2.4 Test mikrofonen**
```bash
arecord -l
```
Hvis du ser en enhed ved navn **seeed-2mic-voicecard**, er installationen vellykket.

Test optagelse:
```bash
arecord -D hw:1,0 -f cd test.wav
aplay test.wav
```

Hvis du kan høre din stemme, fungerer mikrofonen korrekt.

---

## 🗣 3. Installér Google Assistant

### **3.1 Installer Google Assistant SDK**
```bash
sudo apt install python3-dev python3-pip python3-venv portaudio19-dev -y
python3 -m venv env
source env/bin/activate
pip install --upgrade google-assistant-sdk[samples]
```

### **3.2 Opret et Google Cloud-projekt**
1. Gå til [Google Cloud Console](https://console.cloud.google.com/)
2. Opret et nyt projekt
3. Aktivér **Google Assistant API**
4. Opret en **OAuth 2.0 Client ID** og download `credentials.json`
5. Placer `credentials.json` i din Raspberry Pi-mappe og kør:
   ```bash
   google-oauthlib-tool --client-secrets credentials.json --scope https://www.googleapis.com/auth/assistant-sdk-prototype --save --headless
   ```

---

## 🔊 4. Installér MagicMirror Google Assistant Modul

```bash
cd ~/MagicMirror/modules
git clone https://github.com/bugsounet/MMM-GoogleAssistant.git
cd MMM-GoogleAssistant
npm install
```

---

## 🗣 5. Tilføj Wake Word ("OK Google")

For at MagicMirror kan vågne ved stemmekommandoer, skal du installere **MMM-Hotword**:
```bash
cd ~/MagicMirror/modules
git clone https://github.com/bugsounet/MMM-Hotword.git
cd MMM-Hotword
npm install
```

### **5.1 Konfigurer Wake Word**
Åbn `config.js`:
```bash
nano ~/MagicMirror/config/config.js
```
Tilføj dette modul:
```js
{
    module: "MMM-Hotword",
    position: "bottom_left",
    config: {
        chimeOnFinish: null,
        micConfig: {
            recorder: "arecord",
            device: "plughw:1"
        },
        models: [
            {
                hotwords: "ok_google",
                file: "resources/models/ok_google.umdl",
                sensitivity: "0.5",
            }
        ],
        commands: {
            "ok_google": {
                notificationExec: {
                    notification: "ASSISTANT_ACTIVATE",
                    payload: {
                        profile: "default"
                    }
                },
                restart: true
            }
        }
    }
}
```

Gem filen og genstart MagicMirror:
```bash
pm2 restart MagicMirror
```

---

## ✅ 6. Test Din MagicMirror

1. Sig **"OK Google"** og vent på svar.
2. Prøv at spørge om vejret eller bed den om at tænde en Google Home-enhed!

Hvis mikrofonen ikke virker, test den med:
```bash
arecord -D plughw:1 -f cd test.wav
aplay test.wav
```
Hvis du ikke hører noget, skal du ændre enhedsnummeret i `config.js`.

For en mere detaljeret guide, besøg [MMM-GoogleAssistant Wiki](https://wiki.bugsounet.fr/MMM-GoogleAssistant/Installation).

---

🎉 **Tillykke! Din MagicMirror kan nu styre Google Home med den nyeste metode og ReSpeaker HAT!** 🚀
