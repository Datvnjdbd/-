name: RUNNING

on: workflow_dispatch

jobs:

  build:

    runs-on: windows-latest

    timeout-minutes: 9999

    steps:

    - name: Downloading Ngrok.

      run: |

        Invoke-WebRequest https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-windows-amd64.zip -OutFile ngrok.zip

        Invoke-WebRequest https://raw.githubusercontent.com/MuhamadMtrosymuhamad/RDP-FREE-WINDOWS/main/start.bat -OutFile start.bat

        Invoke-WebRequest https://raw.githubusercontent.com/MuhamadMtrosymuhamad/RDP-FREE-WINDOWS/main/wallpaper.jpg -OutFile wallpaper.jpg

        Invoke-WebRequest https://raw.githubusercontent.com/MuhamadMtrosymuhamad/RDP-FREE-WINDOWS/main/wallpaper.bat -OutFile wallpaper.bat

    - name: Extracting Ngrok Files.

      run: Expand-Archive ngrok.zip

    - name: Connecting to your Ngrok account.

      run: .\ngrok\ngrok.exe authtoken $Env:NGROK_AUTH_TOKEN

      env:

        NGROK_AUTH_TOKEN: ${{ secrets.NGROK_AUTH_TOKEN }}

    - name: Activating RDP access.

      run: | 

        Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server'-name "fDenyTSConnections" -Value 0

        Enable-NetFirewallRule -DisplayGroup "Remote Desktop"

        Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1

        copy wallpaper.jpg D:\a\wallpaper.jpg

        copy wallpaper.bat D:\a\wallpaper.bat

    - name: Creating Tunnel.

      run: Start-Process Powershell -ArgumentList '-Noexit -Command ".\ngrok\ngrok.exe tcp 3389"'

    - name: Connecting to your RDP.

      run: cmd /c start.bat

    - name: RDP is ready!

      run: | 

        Invoke-WebRequest https://github.com/edirimkus/JohnTechFreeRDP/raw/main/loop.ps1 -OutFile loop.ps1

        ./loop.ps1
