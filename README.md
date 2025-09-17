# Igual1x1
oraculo chainlink !

Copia el código, compila con Solidity 0.8.20, despliega en Sepolia testnet (actualiza oráculos a direcciones de testnet: BTC/USD = 0x1b44F3514812d835EB1BDB0acB33d4a3, ETH/USD = 0x694AA1769357215DE4FAC081bf1f309aDC325306).

paso2.- Después de crear el contrato BitsBtc 

paso2.- Crea el token mock de Btc para simular el flujo completo
Mock+Btc

2.- Cómo usarlo en Sepolia

1. Despliega el contrato MockBTC  
   Usa Remix o Hardhat en Sepolia. Asegúrate de tener ETH de testnet.

2. Agrega el token a MetaMask  
   Copia la dirección del contrato desplegado y agrégalo como token personalizado.

3. Usa mint() para distribuir BTC de prueba  
   Puedes enviar mBTC a cualquier dirección para simular usuarios.

4. Conecta MockBTC con tu contrato SevenBTC  
   Pasa la dirección de MockBTC como parámetro _btcTokenAddress al constructor de SevenBTC.
