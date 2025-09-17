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

3.-  usuario envíe BTC (EVM) al contrato.
- Reciba:
  - Su BTC de regreso.
  - 1 ⁷BTC fijo.
- Se descuente una comisión de $4000 USD, convertida dinámicamente a BTC usando Chainlink.
- La comisión en BTC se transfiera directamente al propietario del contrato.

---

✅ Versión actualizada del contrato

`solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

interface IERC20BTC {
    function transfer(address to, uint256 amount) external returns (bool);
    function transferFrom(address from, address to, uint256 amount) external returns (bool);
}

contract SevenBTC is ERC20, Ownable {
    AggregatorV3Interface internal priceFeed;
    address public btcTokenAddress;
    uint256 public commissionUSD = 4000 * 1e8; // $4000 con 8 decimales

    constructor(address btcTokenAddress, address priceFeedAddress) ERC20("⁷BTC", "⁷BTC") {
        mint(msg.sender, 100000_000  10 * decimals());
        btcTokenAddress = _btcTokenAddress;
        priceFeed = AggregatorV3Interface(_priceFeedAddress);
    }

    function setCommissionUSD(uint256 newCommissionUSD) external onlyOwner {
        commissionUSD = newCommissionUSD;
    }

    function getBTCPrice() public view returns (uint256) {
        (, int price,,,) = priceFeed.latestRoundData();
        return uint256(price); // BTC/USD con 8 decimales
    }

    function getCommissionInBTC() public view returns (uint256) {
        uint256 btcPrice = getBTCPrice();
        return (commissionUSD * 1e8) / btcPrice; // comisión en satoshis
    }

    function exchangeFixedBTC(uint256 amountBTC) external {
        uint256 commissionBTC = getCommissionInBTC();
        require(amountBTC >= 1e8, "Debe enviar al menos 1 BTC");
        require(amountBTC > commissionBTC, "El monto debe cubrir la comisión");

        IERC20BTC btc = IERC20BTC(btcTokenAddress);

        // Transferir BTC del usuario al contrato
        require(btc.transferFrom(msg.sender, address(this), amountBTC), "Transferencia BTC fallida");

        // Enviar comisión al propietario
        require(btc.transfer(owner(), commissionBTC), "Transferencia de comisión fallida");

        // Reembolsar BTC al usuario (menos comisión)
        uint256 refundBTC = amountBTC - commissionBTC;
        require(btc.transfer(msg.sender, refundBTC), "Reembolso BTC fallido");

        // Entregar 1 ⁷BTC fijo
        _mint(msg.sender, 1  10 * decimals());
    }
}
`

---

🧪 Ejemplo práctico

- Usuario envía 1 BTC (EVM).
- Precio BTC/USD = $27,000 → comisión ≈ 0.14814814 BTC.
- El contrato:
  - Envía 0.14814814 BTC al propietario.
  - Reembolsa 0.85185186 BTC al usuario.
  - Entrega 1 ⁷BTC al usuario.
