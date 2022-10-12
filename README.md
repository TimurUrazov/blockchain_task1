# blockchain_task1
Homework 1 from blockchain course read by Mixbytes

Output of git diff:

```

diff --git a/DividendToken.sol b/DividendToken.sol
index 651290f..74e5f8a 100644
--- a/DividendToken.sol
+++ b/DividendToken.sol
@@ -18,7 +18,7 @@ contract DividendToken is StandardToken, Ownable {
     event PayDividend(address indexed to, uint256 amount);
     event HangingDividend(address indexed to, uint256 amount) ;
     event PayHangingDividend(uint256 amount) ;
-    event Deposit(address indexed sender, uint256 value);
+    event Deposit(address indexed sender, uint256 value, bytes32 comment);
 
     /// @dev parameters of an extra token emission
     struct EmissionInfo {
@@ -37,9 +37,9 @@ contract DividendToken is StandardToken, Ownable {
         }));
     }
 
-    function() external payable {
+    function(bytes32 comment) external payable {
         if (msg.value > 0) {
-            emit Deposit(msg.sender, msg.value);
+            emit Deposit(msg.sender, msg.value, comment);
             m_totalDividends = m_totalDividends.add(msg.value);
         }
     }
diff --git a/ERC20.sol b/ERC20.sol
index 9d0c282..fa6ef08 100644
--- a/ERC20.sol
+++ b/ERC20.sol
@@ -2,6 +2,8 @@
 
 pragma solidity ^0.8.0;
 
+import {DateTime} from "https://github.com/RollaProject/solidity-datetime/blob/master/contracts/DateTime.sol";
+
 import "../../utils/Context.sol";
 import "./IERC20.sol";
 
@@ -30,6 +32,8 @@ import "./IERC20.sol";
  * allowances. See {IERC20-approve}.
  */
 contract ERC20 is Context, IERC20 {
+    using DateTime for uint256;
+
     mapping (address => uint256) private _balances;
 
     mapping (address => mapping (address => uint256)) private _allowances;
@@ -208,7 +212,7 @@ contract ERC20 is Context, IERC20 {
     function _transfer(address sender, address recipient, uint256 amount) internal virtual {
         require(sender != address(0), "ERC20: transfer from the zero address");
         require(recipient != address(0), "ERC20: transfer to the zero address");
-
+        require(block.timestamp.getDayOfWeek() != 6, "ERC20: token can't be transferred on Suturday");
         _beforeTokenTransfer(sender, recipient, amount);
 
         require(_balances[sender] >= amount, "ERC20: transfer amount exceeds balance");
diff --git a/MultiSigWallet.sol b/MultiSigWallet.sol
index 6a777f2..e967086 100644
--- a/MultiSigWallet.sol
+++ b/MultiSigWallet.sol
@@ -22,6 +22,7 @@ contract MultiSigWallet {
      *  Constants
      */
     uint constant public MAX_OWNER_COUNT = 50;
+    uint constant public MAX_TRANSACTION_VALUE = 66;
 
     /*
      *  Storage
@@ -91,6 +92,11 @@ contract MultiSigWallet {
         _;
     }
 
+    modifier validTransactionValue(uint value) {
+        require(value <= MAX_TRANSACTION_VALUE);
+        _;
+    }
+
     /// @dev Fallback function allows to deposit ether.
     function()
         payable
@@ -289,6 +295,7 @@ contract MultiSigWallet {
     function addTransaction(address destination, uint value, bytes data)
         internal
         notNull(destination)
+        validTransactionValue(value)
         returns (uint transactionId)
     {
         transactionId = transactionCount;
diff --git a/README.md b/README.md
index 698c707..e69de29 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +0,0 @@
-# blockchain_task1
-Homework 1 from blockchain course read by Mixbytes

```
