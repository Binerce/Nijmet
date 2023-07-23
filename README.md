# Nijmet
contract MMM is Context, IERC20, Ownable {
    using SafeMath for uint256;
    mapping (address => uint256) private _balances;
    mapping (address => mapping (address => uint256)) private _allowances;
    mapping (address => bool) private _isExcludedFromFee;
    mapping (address => bool) private bots;
    address payable private _taxWallet;
    uint256 firstBlock;

    uint256 private _initialBuyTax=13;
    uint256 private _initialSellTax=12;
    uint256 private _finalBuyTax=1;
    uint256 private _finalSellTax=1;
    uint256 private _reduceBuyTaxAt=12;
    uint256 private _reduceSellTaxAt=12;
    uint256 private _preventSwapBefore=30;
    uint256 private _buyCount=0;

    uint8 private constant _decimals = 9;
    uint256 private constant _tTotal = 420690000000000 * 10**_decimals;
    string private constant _name = unicode"Magic Milady Money";
    string private constant _symbol = unicode"MMM";
    uint256 public _maxTxAmount =   8413800000000 * 10**_decimals;
    uint256 public _maxWalletSize = 8413800000000 * 10**_decimals;
    uint256 public _taxSwapThreshold= 4206900000000 * 10**_decimals;
    uint256 public _maxTaxSwap= 4206900000000 * 10**_decimals;

    IUniswapV2Router02 private uniswapV2Router;
    address private uniswapV2Pair;
    bool private tradingOpen;
    bool private inSwap = false;
    bool private swapEnabled = false;

    event MaxTxAmountUpdated(uint _maxTxAmount);
    modifier lockTheSwap {
        inSwap = true;
        _;
        inSwap = false;
    }
