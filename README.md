# boatlife

App de uma página para checar as condições do mar e do trânsito antes de sair de casa, na região entre Palm Beach e Pompano Beach, FL. Instala na Tela de Início do iPhone como um app comum — sem loja, sem build.

**[cowbyte88.github.io/boatlife](https://cowbyte88.github.io/boatlife/)**

## Aba 🌊 Mar

- Ondas, swell, vento e maré em tempo real, com um veredito simples ("Bom para o barco" / "Boas ondas para surfar" etc.)
- Alerta de **Hurricane Watch** fixo no topo, acima de tudo — só aparece quando há um em vigor pra região
- 4 câmeras ao vivo (Lake Worth, Boynton, Boca Raton e Jupiter Inlet)

Fontes: Open-Meteo Marine & Weather API (ondas/vento), NOAA Tides & Currents — estação Lake Worth Pier (maré), National Weather Service `alerts/active` (Hurricane Watch), Palm Beach County ERM e Jupiter Inlet District (câmeras).

## Aba 🚦 Trânsito

Tempo de viagem atual vs. normal na I-95, partindo de Hypoluxo Island, em duas direções:
- Sul → Boca/Delray
- Norte → Palm Beach Gardens

Sinaliza acidentes, obras e vias fechadas no caminho. Atualiza só quando você abre a aba ou aperta "Atualizar" — sem push, sem gatilho automático.

Dados via TomTom (Routing + Traffic + Incidents), mas a página nunca fala com o TomTom diretamente: ela chama um endpoint de leitura (`/api/status`) no projeto irmão **[car-traffic-alert](https://github.com/cowbyte88/car-traffic-alert)**, que já mantém a chave do TomTom protegida como variável de ambiente na Vercel. Isso evita expor a chave num site estático.

## Arquitetura

```
iPhone (Safari, instalado na Tela de Início)
  -> abre index.html (GitHub Pages, site 100% estático)

     aba Mar:
       -> fetch Open-Meteo Marine API (ondas, swell)
       -> fetch Open-Meteo Weather API (vento)
       -> fetch NOAA CO-OPS (maré, estação 8722670)
       -> fetch api.weather.gov/alerts/active (Hurricane Watch)

     aba Trânsito:
       -> fetch car-traffic-alert.vercel.app/api/status
            -> Vercel Edge Function (sem auth, sem push)
               -> TomTom Geocoding + Routing + Traffic API
               -> chave TOMTOM_API_KEY protegida (env var da Vercel)
```

## Notas

- Site estático, sem build — é só HTML/CSS/JS num único arquivo (`index.html`), publicado via upload direto no GitHub.
- Uso apenas para orientação — sempre confira o boletim marítimo oficial do NWS/Guarda Costeira antes de sair com o barco, e o trânsito ao vivo antes de sair de carro.
