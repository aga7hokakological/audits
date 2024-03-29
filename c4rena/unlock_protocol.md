C4 finding submitted:

 # Handle

aga7hokakological


# Vulnerability details

## Impact
Layout of contract elements should be in following order:
Pragma statements > Import statements > Interfaces > Libraries > Contracts


## Proof of Concept
library Roles {
   struct Role {
       mapping (address => bool) bearer;
   }

   /**
    * @dev Give an account access to this role.
    */
   function add(Role storage role, address account) internal {
       require(!has(role, account), "Roles: account already has role");
       role.bearer[account] = true;
   }

   /**
    * @dev Remove an account's access to this role.
    */
   function remove(Role storage role, address account) internal {
       require(has(role, account), "Roles: account does not have role");
       role.bearer[account] = false;
   }

   /**
    * @dev Check if an account has this role.
    * @return bool
    */
   function has(Role storage role, address account) internal view returns (bool) {
       require(account != address(0), "Roles: account is the zero address");
       return role.bearer[account];
   }
}


// File @openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol@v4.2.0



/**
* @dev Interface of the ERC20 Permit extension allowing approvals to be made via signatures, as defined in
* https://eips.ethereum.org/EIPS/eip-2612[EIP-2612].
*
* Adds the {permit} method, which can be used to change an account's ERC20 allowance (see {IERC20-allowance}) by
* presenting a message signed by the account. By not relying on {IERC20-approve}, the token holder account doesn't
* need to send a transaction, and thus is not required to hold Ether at all.
*/
interface IERC20PermitUpgradeable {
   /**
    * @dev Sets `value` as the allowance of `spender` over ``owner``'s tokens,
    * given ``owner``'s signed approval.
    *
    * IMPORTANT: The same issues {IERC20-approve} has related to transaction
    * ordering also apply here.
    *
    * Emits an {Approval} event.
    *
    * Requirements:
    *
    * - `spender` cannot be the zero address.
    * - `deadline` must be a timestamp in the future.
    * - `v`, `r` and `s` must be a valid `secp256k1` signature from `owner`
    * over the EIP712-formatted function arguments.
    * - the signature must use ``owner``'s current nonce (see {nonces}).
    *
    * For more information on the signature format, see the
    * https://eips.ethereum.org/EIPS/eip-2612#specification[relevant EIP
    * section].
    */
   function permit(
       address owner,
       address spender,
       uint256 value,
       uint256 deadline,
       uint8 v,
       bytes32 r,
       bytes32 s
   ) external;

   /**
    * @dev Returns the current nonce for `owner`. This value must be
    * included whenever a signature is generated for {permit}.
    *
    * Every successful call to {permit} increases ``owner``'s nonce by one. This
    * prevents a signature from being used multiple times.
    */
   function nonces(address owner) external view returns (uint256);

   /**
    * @dev Returns the domain separator used in the encoding of the signature for {permit}, as defined by {EIP712}.
    */
   // solhint-disable-next-line func-name-mixedcase
   function DOMAIN_SEPARATOR() external view returns (bytes32);
}


## Tools Used
Manual analysis




C4 finding submitted:

# Handle

aga7hokakological


# Vulnerability details

## Impact
The functions should be ordered according to their visibility and order and solidity style guide:
constructor > fallback function (if exists) > external > public > internal > private and view/pure functions at last.


## Proof of Concept
abstract contract ERC20PermitUpgradeable is Initializable, ERC20Upgradeable, IERC20PermitUpgradeable, EIP712Upgradeable {
function __ERC20Permit_init_unchained(string memory name) internal initializer {
      _PERMIT_TYPEHASH = keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");
  }

  function __ERC20Permit_init_unsafe(string memory name) internal {
      _PERMIT_TYPEHASH = keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");
  }

  /**
   * @dev See {IERC20Permit-permit}.
   */
  function permit(
      address owner,
      address spender,
      uint256 value,
      uint256 deadline,
      uint8 v,
      bytes32 r,
      bytes32 s
  ) public virtual override {
      require(block.timestamp <= deadline, "ERC20Permit: expired deadline");

      bytes32 structHash = keccak256(abi.encode(_PERMIT_TYPEHASH, owner, spender, value, _useNonce(owner), deadline));

      bytes32 hash = _hashTypedDataV4(structHash);

      address signer = ECDSAUpgradeable.recover(hash, v, r, s);
      require(signer == owner, "ERC20Permit: invalid signature");

      _approve(owner, spender, value);
  }

## Tools Used
Manual analysis