**BaseV1- core**
- L100/108/342/523/526/556/561/562/576/581/586 - It is validated with a require but no message is sent if it reverts, the correct thing to do would be to show a message according to the reason for the error.

- L26/420 - There is commented code that should not be, since it does not provide any value, nor a better understanding of the code.

- L76 - There is an event, claim that is created but never used, this is dead code that shouldn't be there.

- L177/180 - if you set the value inside the if and put a new value in _observation, that value could have been executed in the same block with the last one, so you could make "timeElapsed" return 0 and then when divisions are made an exception would be generated. The solution to this is to validate and display a message if timeElapsed == 0.

- L187/196/224/234/260/268 - The function receives an input and then uses it to do a division, it should be validated if it is == 0 so that it does not generate an exception without a message.

- L331/332/333/334/352/353/355/356 - The burn function has a modifier lock that allows it to validate the reentrancy, but in any case it is not recommended that the check effect interact pattern is not fulfilled, in the code is first transferred and then the balance is updated.

- L555 - The mechanism for changing the admin could be improved by adding the functionality that there is a pending admin and that the address must accept its role.

- L536 - MaxPeriod should be a constant since it will never be modified.


**BaseV1-libs.sol**
- L4 - The erc20 interface does not comply with the standards that exist regarding using interfaces, a more correct name could be IERC20 for the interface.

 **BaseV1-periphery.sol**
- L89 - The mechanism for changing the admin could be improved by adding the functionality that there is a pending admin and that the address must accept its role.

- L90/219/220/300/465/468 - It is validated with a require but no message is sent if it reverts, the correct thing to do would be to show a message according to the reason for the error.

- L55 - The struct route should be written with the first letter in uppercase (Route) in this way it is clear that it is a structure.

- L203/205/206 - It should be validated that the total supply is != 0 and otherwise revert showing a message for the case.

- L370/371/373 - In the for cycle an array is iterated and internally it is used to go through another array as well, this can generate errors since if they do not have the same length it could generate exceptions and incorrect values.

