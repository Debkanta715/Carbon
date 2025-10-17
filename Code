
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/// @title GreenCarbon - A Carbon Credit Token and Trading Platform
/// @author 
/// @notice Enables certified carbon credit issuance, trading, and retirement
contract GreenCarbon {
    string public name = "GreenCarbon";
    string public symbol = "GCC";
    uint8 public decimals = 18;

    address public admin;

    // Balances of each account
    mapping(address => uint256) public balanceOf;

    // Allowance for trading
    mapping(address => mapping(address => uint256)) public allowance;

    // Issuers who can mint credits
    mapping(address => bool) public certifiedIssuers;

    // Total supply in circulation
    uint256 public totalSupply;

    // Events
    event Transfer(address indexed from, address indexed to, uint256 amount);
    event Approval(address indexed owner, address indexed spender, uint256 amount);
    event IssuerAdded(address indexed issuer);
    event CreditsMinted(address indexed issuer, address indexed to, uint256 amount);
    event CreditsBurned(address indexed company, uint256 amount);

    modifier onlyAdmin() {
        require(msg.sender == admin, "Not admin");
        _;
    }

    modifier onlyIssuer() {
        require(certifiedIssuers[msg.sender], "Not a certified issuer");
        _;
    }

    constructor() {
        admin = msg.sender;
    }

    /// @notice Add a certified issuer who can mint carbon credits
    function addCertifiedIssuer(address _issuer) external onlyAdmin {
        certifiedIssuers[_issuer] = true;
        emit IssuerAdded(_issuer);
    }

    /// @notice Mint carbon credits to a verified buyer or marketplace
    function mintCredits(address _to, uint256 _amount) external onlyIssuer {
        require(_to != address(0), "Invalid address");
        balanceOf[_to] += _amount;
        totalSupply += _amount;
        emit CreditsMinted(msg.sender, _to, _amount);
        emit Transfer(address(0), _to, _amount);
    }

    /// @notice Approve a buyer/trader to transfer credits on your behalf
    function approve(address _spender, uint256 _amount) external {
        allowance[msg.sender][_spender] = _amount;
        emit Approval(msg.sender, _spender, _amount);
    }

    /// @notice Transfer credits to another address
    function transfer(address _to, uint256 _amount) external returns (bool) {
        require(balanceOf[msg.sender] >= _amount, "Insufficient balance");
        _transfer(msg.sender, _to, _amount);
        return true;
    }

    /// @notice TransferFrom (used by marketplaces/traders)
    function transferFrom(address _from, address _to, uint256 _amount) external returns (bool) {
        require(balanceOf[_from] >= _amount, "Insufficient balance");
        require(allowance[_from][msg.sender] >= _amount, "Allowance exceeded");
        allowance[_from][msg.sender] -= _amount;
        _transfer(_from, _to, _amount);
        return true;
    }

    function _transfer(address _from, address _to, uint256 _amount) internal {
        require(_to != address(0), "Invalid recipient");
        balanceOf[_from] -= _amount;
        balanceOf[_to] += _amount;
        emit Transfer(_from, _to, _amount);
    }

    /// @notice Burn (retire) carbon credits permanently
    function burn(uint256 _amount) external {
        require(balanceOf[msg.sender] >= _amount, "Insufficient balance");
        balanceOf[msg.sender] -= _amount;
        totalSupply -= _amount;
        emit CreditsBurned(msg.sender, _amount);
        emit Transfer(msg.sender, address(0), _amount);
    }
}
