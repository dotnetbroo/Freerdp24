Steps To Create Windows Server
ro'yxatdan o'ting : https://github.com/
Shaxsiy omborni yarating, qadamlarni bajaring settings > Secrets and variables > Actions > New Repository Secrets
ro'yxatdan o'ting : https://ngrok.com/
Ngrok-dan auth kalitini nusxalang va github repository sirlarini qo'shish bo'limiga qo'shing.
Yangi ish oqimini qo'lda o'rnating. Quyidagi kodni main.yml-ga qo'ying va o'zgarishlar qiling
name: CI

on: [push, workflow_dispatch]

jobs:
  build:

    runs-on: windows-latest

    steps:
    - name: Download
      run: Invoke-WebRequest https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-windows-amd64.zip -OutFile ngrok.zip
    - name: Extract
      run: Expand-Archive ngrok.zip
    - name: Auth
      run: .\ngrok\ngrok.exe authtoken $Env:NGROK_AUTH_TOKEN
      env:
        NGROK_AUTH_TOKEN: ${{ secrets.NGROK_AUTH_TOKEN }}
    - name: Enable TS
      run: Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server'-name "fDenyTSConnections" -Value 0
    - run: Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
    - run: Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1
    - run: Set-LocalUser -Name "runneradmin" -Password (ConvertTo-SecureString -AsPlainText "P@ssw0rd!" -Force)
    - name: Create Tunnel
      run: .\ngrok\ngrok.exe tcp 3389

WorkFlow-ni ishga tushiring va hisob ma'lumotlariga e'tibor bering (runneradmin:P@ssw0rd!)
Ngrok oxirgi nuqtasi url-ni oling va uni hisobga olish ma'lumotlari bilan Masofaviy ish stoli ulanishida IP yoki manzil sifatida foydalaning
