Para ver los atajos del teclado, presiona el signo de interrogación
Ver atajos de teclado

Crea un contrato ERC20 llamado x2 Ethereum  (+ETH)
Con 50 millones de unidades.
Funciones;El propietario podrá crear más (+ETH) cuando quiera.Igual valor que Ethereum

Aquí tienes un contrato ERC20 en Solidity para el token "x2 Ethereum" (+ETH) con 50 millones de unidades iniciales, la capacidad del propietario para mintear más tokens, y configurado con 18 decimales para alinearse con Ethereum. Dado que mencionaste "igual valor que Ethereum", asumo que te refieres a que el token debe tener el mismo número de decimales (18) que ETH para mantener consistencia en la unidad de medida, aunque el valor de mercado real dependerá de factores externos.solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract x2Ethereum is ERC20, Ownable {
    uint256 public constant INITIAL_SUPPLY = 50_000_000 * 10**18; // 50 millones de tokens con 18 decimales

    constructor() ERC20("x2 Ethereum", "+ETH") Ownable(msg.sender) {
        _mint(msg.sender, INITIAL_SUPPLY);
    }

    // Función para que el propietario cree más tokens
    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }

    // Define 18 decimales, igual que Ethereum
    function decimals() public pure override returns (uint8) {
        return 18;
    }
}

Detalles del contrato:Estándar ERC20: Utiliza la implementación de OpenZeppelin para ERC20, que incluye funciones estándar como transfer, approve, balanceOf, etc.
Propietario: Hereda de Ownable, restringiendo el minting al propietario del contrato (quien lo despliega inicialmente).
Suministro inicial: 50 millones de tokens (+ETH) con 18 decimales, asignados al propietario en el constructor.
Minting: La función mint permite al propietario crear más tokens en cualquier momento, especificando la cantidad y el destinatario.
Decimales: Configurado con 18 decimales, igual que Ethereum, para mantener consistencia en la representación de valores.
Nombre y símbolo:Nombre: "x2 Ethereum"
Símbolo: "+ETH"

Aclaraciones:Igual valor que Ethereum: El contrato usa 18 decimales como ETH, pero el "valor" en términos de precio (por ejemplo, en USD o ETH) no está controlado por el contrato, ya que depende del mercado o de un mecanismo externo (como un oráculo). Si necesitas que el token esté vinculado al precio de ETH, sería necesario integrar un oráculo como Chainlink, pero esto no se incluyó porque no lo especificaste en detalle.
Dependencias: Requiere las librerías de OpenZeppelin. Instálalas con:bash

npm install @openzeppelin/contracts

Seguridad: El propietario tiene control total sobre el minting. Considera agregar restricciones (como un límite de tiempo o cantidad) si deseas evitar emisiones excesivas.
Despliegue: Usa un entorno como Remix, Hardhat o Foundry para compilar y desplegar. El propietario será la cuenta que despliegue el contrato.

Pasos para usar:Configura tu entorno de desarrollo (Remix es una buena opción para pruebas rápidas).
Importa las librerías de OpenZeppelin.
Compila y despliega en una red de prueba (como Sepolia) o en la red principal.
Verifica el contrato en Etherscan para transparencia.

Si necesitas funcionalidades adicionales, como un oráculo para vincular el precio, una función de quema de tokens, o límites en el minting, por favor indícalas y ajustaré el código. 

Explicar oráculos Chainlink

Otros estándares ERC

Agrega el oráculo para vincular el precio para;
Sepolia 
Mainnet

Searched for "Chainlink ETH/USD price feed Mainnet address"
5 resultados

Mostrar todo (2)

Aquí tienes el contrato ERC20 actualizado para "x2 Ethereum" (+ETH) con la integración de un oráculo de Chainlink. He agregado la funcionalidad para vincular el precio del token al de Ethereum (ETH/USD), utilizando el oráculo de Chainlink que proporciona el precio de ETH en USD en tiempo real. Esto permite que el contrato consulte el precio actual de ETH, lo que puede usarse para mantener una equivalencia de valor (por ejemplo, en funciones de conversión o validaciones de transacciones).Explicación de los cambios:Oráculo de Chainlink: Se integra AggregatorV3Interface para consultar el precio de ETH/USD. El contrato incluye una función getLatestETHPrice() que devuelve el precio actual de ETH en USD (con 8 decimales, como proporciona Chainlink).
Direcciones del oráculo: Para adaptarse a Sepolia (testnet) y Mainnet, he hecho que la dirección del oráculo sea configurable en el constructor. Esto evita hardcodear valores y permite desplegar el mismo contrato en ambas redes:Sepolia: Dirección del oráculo ETH/USD: 0x694AA1769357215DE4FAC081bf1f309aDC325306 (confirmado desde la documentación oficial de Chainlink).
Mainnet: Dirección del oráculo ETH/USD: 0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419 (confirmado desde la documentación oficial de Chainlink).

Vinculación de valor: El "valor igual que Ethereum" se interpreta como acceso al precio de ETH para que el token (+ETH) pueda usarlo en lógica de negocio (por ejemplo, para swaps, validaciones o cálculos). Si necesitas algo más específico (como un mecanismo de backing o conversión automática), indícalo para refinarlo. Nota: El precio real de mercado del token dependerá de factores externos; el oráculo solo proporciona datos de ETH.
Suministro y minting: Mantenido igual, con 50 millones de unidades iniciales y minting ilimitado para el propietario.
Dependencias: Ahora requiere también las librerías de Chainlink además de OpenZeppelin. Instálalas con:bash

npm install @openzeppelin/contracts @chainlink/contracts

Código del contrato:solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract x2Ethereum is ERC20, Ownable {
    uint256 public constant INITIAL_SUPPLY = 50_000_000 * 10**18; // 50 millones de tokens con 18 decimales
    
    AggregatorV3Interface internal priceFeed; // Oráculo para ETH/USD

    constructor(address _priceFeedAddress) ERC20("x2 Ethereum", "+ETH") Ownable(msg.sender) {
        priceFeed = AggregatorV3Interface(_priceFeedAddress);
        _mint(msg.sender, INITIAL_SUPPLY);
    }

    // Función para que el propietario cree más tokens
    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }

    // Función para obtener el precio actual de ETH en USD desde el oráculo
    function getLatestETHPrice() public view returns (int) {
        (
            /* uint80 roundID */,
            int price,
            /* uint startedAt */,
            /* uint timeStamp */,
            /* uint80 answeredInRound */
        ) = priceFeed.latestRoundData();
        return price; // Precio con 8 decimales (ej: 300000000000 para $3000)
    }

    // Define 18 decimales, igual que Ethereum
    function decimals() public pure override returns (uint8) {
        return 18;
    }
}

Cómo desplegar en Sepolia y Mainnet:Entorno de desarrollo: Usa Hardhat, Foundry o Remix. Asegúrate de tener las dependencias instaladas.
Constructor: Al desplegar, pasa la dirección del oráculo como parámetro:Para Sepolia (testnet):solidity

// En tu script de despliegue o Remix
new x2Ethereum(0x694AA1769357215DE4FAC081bf1f309aDC325306);

Obtén ETH de prueba de un faucet como Sepolia Faucet.

Para Mainnet:solidity

// En tu script de despliegue o Remix
new x2Ethereum(0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419);

Usa ETH real; considera costos de gas.

Verificación: Después de desplegar, verifica el contrato en Etherscan (Sepolia o Mainnet) para transparencia.
Pruebas: En Sepolia, puedes llamar a getLatestETHPrice() para ver el precio simulado. En Mainnet, obtendrás el precio real de ETH en USD.

Notas adicionales:Precisión del oráculo: El precio de ETH viene con 8 decimales (ej: 1 ETH = $3000 → 300000000000). Puedes escalarlo si necesitas comparaciones con el token (que tiene 18 decimales).
Seguridad: El oráculo es descentralizado y confiable, pero siempre valida el updatedAt en producción para asegurar frescura de datos (puedes agregar esta lógica si lo necesitas).
Costos: Consultas al oráculo son de solo lectura (view), por lo que no consumen gas extra en transacciones off-chain, pero en on-chain sí.
Limitaciones: Si "igual valor" implica un stablecoin backed por ETH (ej: mint/burn basado en collateral), necesitaríamos agregar lógica de DeFi (como Aave o Uniswap). El oráculo actual solo proporciona el precio para consultas.

Si necesitas más funciones (ej: conversión de tokens a ETH equivalente, límites en minting basados en precio, o integración con otros oráculos), ¡házmelo saber para ajustarlo! 

Agrega el oráculo de sepolia en el contrato

Entiendo que deseas modificar el contrato ERC20 "x2 Ethereum" (+ETH) para incluir explícitamente el oráculo de Chainlink para la red de prueba Sepolia, asegurando que funcione correctamente en ese entorno. La dirección del oráculo ETH/USD para Sepolia ya fue mencionada (0x694AA1769357215DE4FAC081bf1f309aDC325306), y el contrato anterior ya permite configurarla en el constructor. Sin embargo, para hacerlo más específico y enfocado en Sepolia, actualizaré el contrato para incluir la dirección del oráculo de Sepolia directamente en el código, junto con una breve explicación de cómo usarlo.El contrato seguirá teniendo:50 millones de unidades iniciales.
Capacidad del propietario para mintear más tokens.
18 decimales (igual que Ethereum).
Integración con el oráculo de Chainlink para obtener el precio de ETH/USD en Sepolia.

Código del contrato con oráculo para Sepolia:solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract x2Ethereum is ERC20, Ownable {
    uint256 public constant INITIAL_SUPPLY = 50_000_000 * 10**18; // 50 millones de tokens con 18 decimales
    
    AggregatorV3Interface internal priceFeed; // Oráculo para ETH/USD

    // Dirección del oráculo ETH/USD para Sepolia
    address public constant SEPOLIA_PRICE_FEED = 0x694AA1769357215DE4FAC081bf1f309aDC325306;

    constructor() ERC20("x2 Ethereum", "+ETH") Ownable(msg.sender) {
        priceFeed = AggregatorV3Interface(SEPOLIA_PRICE_FEED);
        _mint(msg.sender, INITIAL_SUPPLY);
    }

    // Función para que el propietario cree más tokens
    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }

    // Función para obtener el precio actual de ETH en USD desde el oráculo
    function getLatestETHPrice() public view returns (int) {
        (
            /* uint80 roundID */,
            int price,
            /* uint startedAt */,
            /* uint timeStamp */,
            /* uint80 answeredInRound */
        ) = priceFeed.latestRoundData();
        return price; // Precio con 8 decimales (ej: 300000000000 para $3000)
    }

    // Define 18 decimales, igual que Ethereum
    function decimals() public pure override returns (uint8) {
        return 18;
    }
}

Cambios realizados:Oráculo para Sepolia: La dirección del oráculo ETH/USD para Sepolia (0x694AA1769357215DE4FAC081bf1f309aDC325306) está ahora hardcodeada como una constante (SEPOLIA_PRICE_FEED) y se usa en el constructor para inicializar el priceFeed. Esto asegura que el contrato esté configurado específicamente para Sepolia.
Sin cambios en otras funcionalidades:Suministro inicial: 50 millones de tokens (+ETH) con 18 decimales.
Minting: El propietario puede crear más tokens cuando desee.
Decimales: 18, igual que Ethereum.
Oráculo: La función getLatestETHPrice() devuelve el precio de ETH en USD con 8 decimales, como lo proporciona Chainlink.

Cómo desplegar en Sepolia:Instala dependencias: Asegúrate de tener las librerías de OpenZeppelin y Chainlink instaladas:bash

npm install @openzeppelin/contracts @chainlink/contracts

Configura tu entorno: Usa Remix, Hardhat o Foundry. Configura una cuenta con ETH de prueba (puedes obtenerlo de un faucet como Sepolia Faucet).
Despliega el contrato:En Remix: Copia el código, selecciona la red Sepolia en el entorno, y despliega sin parámetros adicionales (la dirección del oráculo ya está en el código).
En Hardhat: Configura un script de despliegue como el siguiente:javascript

const hre = require("hardhat");

async function main() {
    const x2Ethereum = await hre.ethers.deployContract("x2Ethereum");
    await x2Ethereum.waitForDeployment();
    console.log(`x2Ethereum desplegado en: ${x2Ethereum.target}`);
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});

Asegúrate de configurar el archivo hardhat.config.js con la red Sepolia y tu clave privada.

Prueba el oráculo:Llama a getLatestETHPrice() para obtener el precio de ETH en USD. En Sepolia, este será un valor simulado proporcionado por el oráculo de Chainlink.

Verifica en Sepolia Etherscan: Usa Sepolia Etherscan para verificar el contrato después de desplegarlo.

Notas importantes:Oráculo en Sepolia: El oráculo ETH/USD en Sepolia es mantenido por Chainlink y proporciona datos de prueba. Los precios no son reales, pero son útiles para testing.
Vinculación al valor de ETH: El contrato incluye el precio de ETH vía el oráculo, pero el "valor igual que Ethereum" depende de cómo uses el precio. Por ejemplo, podrías crear una función que ajuste las transferencias o minting basado en el precio de ETH, pero esto no está implementado porque no se especificó. Si quieres lógica adicional (ej: emitir tokens según el valor en USD de ETH), indícalos.
Seguridad:El oráculo es confiable, pero en producción verifica la frescura del precio (usando updatedAt de latestRoundData) para evitar datos obsoletos.
El propietario tiene control total sobre el minting. Considera agregar límites si es necesario.

Mainnet: Este contrato está configurado para Sepolia. Si deseas usarlo en Mainnet, cambia la constante SEPOLIA_PRICE_FEED por 0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419 (oráculo ETH/USD de Mainnet).

Si necesitas agregar funciones específicas (como usar el precio del oráculo para limitar el minting, permitir swaps basados en el precio, o cualquier otra lógica), por favor detállalo y ajustaré el contrato. 

más sobre Chainlink

otros oráculos DeFi

Agrega la función de Airdrop, donde el usuario envía 1 Ethereum a la dirección del creador x2Ethereum, y el usuario recibe 1.5 x2Ethereum

A continuación, te proporciono el contrato ERC20 "x2 Ethereum" (+ETH) actualizado con una función de airdrop que permite a los usuarios enviar 1 ETH al creador del contrato (propietario) y recibir 1.5 tokens x2Ethereum (+ETH) a cambio. El contrato mantiene las características previas:50 millones de tokens iniciales.
Capacidad del propietario para mintear más tokens.
18 decimales (igual que Ethereum).
Oráculo de Chainlink para el precio ETH/USD en Sepolia.
La función de airdrop asegura que el contrato tenga suficientes tokens para distribuir y valida que se envíe exactamente 1 ETH.

Código del contrato actualizado:solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract x2Ethereum is ERC20, Ownable {
    uint256 public constant INITIAL_SUPPLY = 50_000_000 * 10**18; // 50 millones de tokens con 18 decimales
    uint256 public constant AIRDROP_AMOUNT = 1_500_000_000_000_000_000; // 1.5 x2Ethereum (1.5 * 10^18)
    uint256 public constant REQUIRED_ETH = 1 ether; // 1 ETH requerido para el airdrop
    
    AggregatorV3Interface internal priceFeed; // Oráculo para ETH/USD
    address public constant SEPOLIA_PRICE_FEED = 0x694AA1769357215DE4FAC081bf1f309aDC325306;

    constructor() ERC20("x2 Ethereum", "+ETH") Ownable(msg.sender) {
        priceFeed = AggregatorV3Interface(SEPOLIA_PRICE_FEED);
        _mint(address(this), INITIAL_SUPPLY); // Tokens iniciales se mintean al contrato
    }

    // Función para que el propietario cree más tokens
    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }

    // Función para obtener el precio actual de ETH en USD desde el oráculo
    function getLatestETHPrice() public view returns (int) {
        (
            /* uint80 roundID */,
            int price,
            /* uint startedAt */,
            /* uint timeStamp */,
            /* uint80 answeredInRound */
        ) = priceFeed.latestRoundData();
        return price; // Precio con 8 decimales (ej: 300000000000 para $3000)
    }

    // Función de airdrop: usuario envía 1 ETH y recibe 1.5 x2Ethereum
    function airdrop() public payable {
        require(msg.value == REQUIRED_ETH, "Must send exactly 1 ETH");
        require(balanceOf(address(this)) >= AIRDROP_AMOUNT, "Contract lacks sufficient x2Ethereum for airdrop");

        // Transfiere 1 ETH al propietario
        (bool sent, ) = payable(owner()).call{value: msg.value}("");
        require(sent, "Failed to send ETH to owner");

        // Transfiere 1.5 x2Ethereum al usuario
        _transfer(address(this), msg.sender, AIRDROP_AMOUNT);
    }

    // Define 18 decimales, igual que Ethereum
    function decimals() public pure override returns (uint8) {
        return 18;
    }

    // Función para que el propietario retire tokens del contrato (opcional)
    function withdrawTokens(uint256 amount) public onlyOwner {
        require(balanceOf(address(this)) >= amount, "Insufficient tokens in contract");
        _transfer(address(this), msg.sender, amount);
    }

    // Función para recibir ETH directamente (necesaria para el airdrop)
    receive() external payable {}
}

Cambios y detalles de la función de airdrop:Función airdrop:Entrada: El usuario debe enviar exactamente 1 ETH (msg.value == 1 ether).
Salida: El contrato transfiere 1.5 x2Ethereum (1.5 * 10^18 wei, equivalente a 1.5 tokens con 18 decimales) al usuario.
Validaciones:Verifica que se envíe exactamente 1 ETH.
Asegura que el contrato tenga suficientes tokens para el airdrop (balanceOf(address(this)) >= AIRDROP_AMOUNT).
Transfiere el ETH recibido al propietario (owner()).
Transfiere los tokens al usuario usando _transfer.

Suministro inicial:Los 50 millones de tokens iniciales ahora se mintean al contrato (address(this)) en lugar de al propietario. Esto asegura que el contrato tenga tokens disponibles para el airdrop.
El propietario puede usar la función withdrawTokens para recuperar tokens del contrato si es necesario.

Constantes:AIRDROP_AMOUNT: Define 1.5 x2Ethereum (1.5 * 10^18 wei).
REQUIRED_ETH: Define 1 ETH (1 * 10^18 wei).

Función receive:Agregada para permitir que el contrato acepte ETH directamente (necesario para la función airdrop).

Función withdrawTokens:Permite al propietario retirar tokens del contrato (por ejemplo, si sobran tokens o para reabastecer el contrato manualmente).

Oráculo:Se mantiene el oráculo ETH/USD para Sepolia (0x694AA1769357215DE4FAC081bf1f309aDC325306). Aunque no se usa directamente en el airdrop, está disponible para otras lógicas si deseas vincular el precio de ETH a otras funciones.

Cómo desplegar y usar en Sepolia:Instala dependencias:bash

npm install @openzeppelin/contracts @chainlink/contracts

Configura el entorno:Usa Remix, Hardhat o Foundry.
Obtén ETH de prueba desde un faucet como Sepolia Faucet.

Despliega el contrato:En Remix: Copia el código, selecciona la red Sepolia, y despliega sin parámetros (el oráculo de Sepolia ya está configurado).
En Hardhat: Usa un script como:javascript

const hre = require("hardhat");

async function main() {
    const x2Ethereum = await hre.ethers.deployContract("x2Ethereum");
    await x2Ethereum.waitForDeployment();
    console.log(`x2Ethereum desplegado en: ${x2Ethereum.target}`);
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1);
});

Configura hardhat.config.js con la red Sepolia y tu clave privada.

Prueba el airdrop:Llama a la función airdrop enviando exactamente 1 ETH (en Remix, configura "Value" a 1 ETH).
Verifica que recibas 1.5 x2Ethereum y que el ETH se transfiera al propietario.
Usa getLatestETHPrice() para consultar el precio de ETH si necesitas integrarlo en otras lógicas.

Verifica en Etherscan:Verifica el contrato en Sepolia Etherscan para transparencia.

Notas importantes:Saldo del contrato: El contrato debe tener suficientes tokens (+ETH) para los airdrops. Inicialmente, los 50 millones de tokens están en el contrato, pero si se agotan, el propietario debe mintear más tokens al contrato (mint(address(this), amount)).
Seguridad:La función airdrop valida el monto exacto de ETH (1 ETH) para evitar errores.
El propietario debe monitorear el saldo de tokens del contrato para evitar fallos en el airdrop.
Considera agregar un límite de airdrops por usuario (por ejemplo, usando un mapping para rastrear quién ha participado) si quieres evitar abusos.

Oráculo: Aunque el oráculo está incluido, no se usa directamente en el airdrop. Si deseas que el airdrop dependa del precio de ETH (por ejemplo, ajustar la cantidad de tokens según el precio en USD), indícalos para agregar esa lógica.
Mainnet: Este contrato está configurado para Sepolia. Para Mainnet, cambia SEPOLIA_PRICE_FEED por 0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419 y prueba primero en Sepolia para evitar costos altos.

Ejemplo de uso del airdrop:Un usuario envía 1 ETH al contrato llamando a airdrop (o enviando ETH directamente con la función receive activando el airdrop).
El contrato verifica:Que se envió exactamente 1 ETH.
Que hay suficientes tokens (1.5 x2Ethereum) en el contrato.

El contrato transfiere 1 ETH al propietario y 1.5 x2Ethereum al usuario.

Si necesitas ajustes, como:Limitar airdrops por usuario.
Vincular la cantidad de tokens al precio de ETH (usando el oráculo).
Agregar un límite total de airdrops.
Adaptar el contrato para Mainnet.
Por favor, detállalo y lo implementaré. 

más sobre oráculos

otros contratos ERC20

más claro

Agrega otra función;El usuario paga una comisión del 10% de 1 Ethereum por el Airdrop, a la vez la comisión va a la dirección del propietario de x2Ethereum

He actualizado el contrato ERC20 "x2 Ethereum" (+ETH) para incluir una función de airdrop modificada que cobra una comisión del 10% sobre el 1 ETH enviado por el usuario, transfiriendo esta comisión al propietario del contrato. El usuario sigue recibiendo 1.5 x2Ethereum por enviar 1 ETH, pero ahora el contrato separa el 10% (0.1 ETH) como comisión para el propietario, y el resto (0.9 ETH) también va al propietario (como en el diseño anterior). El contrato mantiene las características previas:50 millones de tokens iniciales.
Capacidad del propietario para mintear más tokens.
18 decimales (igual que Ethereum).
Oráculo de Chainlink para el precio ETH/USD en Sepolia.

Código del contrato actualizado:solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract x2Ethereum is ERC20, Ownable {
    uint256 public constant INITIAL_SUPPLY = 50_000_000 * 10**18; // 50 millones de tokens con 18 decimales
    uint256 public constant AIRDROP_AMOUNT = 1_500_000_000_000_000_000; // 1.5 x2Ethereum (1.5 * 10^18)
    uint256 public constant REQUIRED_ETH = 1 ether; // 1 ETH requerido para el airdrop
    uint256 public constant FEE_PERCENT = 10; // Comisión del 10%
    uint256 public constant FEE_AMOUNT = REQUIRED_ETH * FEE_PERCENT / 100; // 0.1 ETH

    AggregatorV3Interface internal priceFeed; // Oráculo para ETH/USD
    address public constant SEPOLIA_PRICE_FEED = 0x694AA1769357215DE4FAC081bf1f309aDC325306;

    constructor() ERC20("x2 Ethereum", "+ETH") Ownable(msg.sender) {
        priceFeed = AggregatorV3Interface(SEPOLIA_PRICE_FEED);
        _mint(address(this), INITIAL_SUPPLY); // Tokens iniciales se mintean al contrato
    }

    // Función para que el propietario cree más tokens
    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }

    // Función para obtener el precio actual de ETH en USD desde el oráculo
    function getLatestETHPrice() public view returns (int) {
        (
            /* uint80 roundID */,
            int price,
            /* uint startedAt */,
            /* uint timeStamp */,
            /* uint80 answeredInRound */
        ) = priceFeed.latestRoundData();
        return price; // Precio con 8 decimales (ej: 300000000000 para $3000)
    }

    // Función de airdrop: usuario envía 1 ETH, paga 10% de comisión, recibe 1.5 x2Ethereum
    function airdrop() public payable {
        require(msg.value == REQUIRED_ETH, "Must send exactly 1 ETH");
        require(balanceOf(address(this)) >= AIRDROP_AMOUNT, "Contract lacks sufficient x2Ethereum for airdrop");

        // Calcula la cantidad después de la comisión
        uint256 amountAfterFee = msg.value - FEE_AMOUNT; // 0.9 ETH
        uint256 fee = FEE_AMOUNT; // 0.1 ETH

        // Transfiere la comisión (0.1 ETH) y el resto (0.9 ETH) al propietario
        (bool sentFee, ) = payable(owner()).call{value: fee}("");
        require(sentFee, "Failed to send fee to owner");
        (bool sentRest, ) = payable(owner()).call{value: amountAfterFee}("");
        require(sentRest, "Failed to send remaining ETH to owner");

        // Transfiere 1.5 x2Ethereum al usuario
        _transfer(address(this), msg.sender, AIRDROP_AMOUNT);
    }

    // Define 18 decimales, igual que Ethereum
    function decimals() public pure override returns (uint8) {
        return 18;
    }

    // Función para que el propietario retire tokens del contrato
    function withdrawTokens(uint256 amount) public onlyOwner {
        require(balanceOf(address(this)) >= amount, "Insufficient tokens in contract");
        _transfer(address(this), msg.sender, amount);
    }

    // Función para recibir ETH directamente
    receive() external payable {}
}

Cambios realizados:Comisión del 10%:Se agregó la constante FEE_PERCENT = 10 para definir el 10% de comisión.
Se calculó FEE_AMOUNT = REQUIRED_ETH * FEE_PERCENT / 100, que equivale a 0.1 ETH.
En la función airdrop:Se valida que el usuario envíe exactamente 1 ETH (REQUIRED_ETH).
Se calcula el monto después de la comisión (amountAfterFee = 0.9 ETH).
Se transfiere la comisión (0.1 ETH) y el resto (0.9 ETH) al propietario en dos transacciones separadas para claridad.
El usuario recibe 1.5 x2Ethereum (AIRDROP_AMOUNT).

Mantenimiento de características previas:Suministro inicial: 50 millones de tokens minteados al contrato (address(this)) para soportar los airdrops.
Minting: El propietario puede mintear más tokens cuando desee.
Oráculo: Incluye el oráculo ETH/USD para Sepolia (0x694AA1769357215DE4FAC081bf1f309aDC325306), aunque no se usa en el airdrop.
WithdrawTokens: Permite al propietario retirar tokens del contrato.
Receive: Permite que el contrato acepte ETH directamente.

Cómo desplegar y usar en Sepolia:Instala dependencias:bash

npm install @openzeppelin/contracts @chainlink/contracts

Configura el entorno:Usa Remix, Hardhat o Foundry.
Obtén ETH de prueba desde un faucet como Sepolia Faucet.

Despliega el contrato:En Remix:Copia el código, selecciona la red Sepolia, y despliega sin parámetros (el oráculo de Sepolia está hardcodeado).
Configura el compilador a una versión compatible con ^0.8.20.

En Hardhat:javascript

const hre = require("hardhat");

async function main() {
    const x2Ethereum = await hre.ethers.deployContract("x2Ethereum");
    await x2Ethereum.waitForDeployment();
    console.log(`x2Ethereum desplegado en: ${x2Ethereum.target}`);
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1);
});

Configura hardhat.config.js con la red Sepolia y tu clave privada.

Prueba el airdrop:Llama a la función airdrop enviando exactamente 1 ETH (en Remix, configura "Value" a 1 ETH).
Verifica que:El propietario recibe 0.1 ETH (comisión) + 0.9 ETH (resto) = 1 ETH.
El usuario recibe 1.5 x2Ethereum.

Usa getLatestETHPrice() si necesitas el precio de ETH para otras lógicas.

Verifica en Etherscan:Verifica el contrato en Sepolia Etherscan para transparencia.

Ejemplo de flujo del airdrop:Un usuario envía 1 ETH al contrato llamando a airdrop.
El contrato:Valida que msg.value == 1 ETH y que hay suficientes tokens (1.5 x2Ethereum).
Transfiere 0.1 ETH (comisión) al propietario.
Transfiere 0.9 ETH (resto) al propietario.
Transfiere 1.5 x2Ethereum al usuario.

Notas importantes:Saldo de tokens: El contrato debe tener suficientes tokens para los airdrops (inicialmente 50 millones). Si se agotan, el propietario debe mintear más al contrato (mint(address(this), amount)).
Comisión: La separación de la transferencia en dos partes (comisión y resto) es para claridad. Si prefieres una sola transferencia, puedo simplificarlo.
Seguridad:La función airdrop valida el monto exacto de ETH (1 ETH).
Usa call para transferencias de ETH, que es más seguro que transfer (evita problemas con límites de gas).
Considera agregar un límite de airdrops por usuario (usando un mapping(address => bool)) para evitar abusos.

Oráculo: No se usa en el airdrop, pero está disponible. Si quieres que la cantidad de tokens (1.5 x2Ethereum) dependa del precio de ETH en USD, puedo agregar esa lógica.
Mainnet: Este contrato está configurado para Sepolia. Para Mainnet, cambia SEPOLIA_PRICE_FEED por 0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419 y prueba primero en Sepolia para evitar costos altos.

Si necesitas ajustes, como:Limitar airdrops por usuario.
Usar el oráculo para ajustar la cantidad de tokens según el precio de ETH.
Simplificar la transferencia de ETH al propietario.
Agregar soporte explícito para Mainnet.
Por favor, detállalo y lo implementaré. 

explicar oráculo Chainlink

otros oráculos blockchain

