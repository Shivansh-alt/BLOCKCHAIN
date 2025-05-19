## que.1 Create a voting system with multiple candidates. Each address can vote only once.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract VotingSystem {
    struct Candidate {
        string name;
        uint voteCount;
    }
    
    Candidate[] public candidates;
    mapping(address => bool) public hasVoted;
    address public owner;
    
    constructor(string[] memory _candidateNames) {
        owner = msg.sender;
        for(uint i = 0; i < _candidateNames.length; i++) {
            candidates.push(Candidate({
                name: _candidateNames[i],
                voteCount: 0
            }));
        }
    }
    
    function vote(uint _candidateIndex) public {
        require(!hasVoted[msg.sender], "You have already voted");
        require(_candidateIndex < candidates.length, "Invalid candidate");
        
        candidates[_candidateIndex].voteCount++;
        hasVoted[msg.sender] = true;
    }
    
    function getWinner() public view returns (string memory) {
        uint winningVoteCount = 0;
        uint winningIndex = 0;
        
        for(uint i = 0; i < candidates.length; i++) {
            if(candidates[i].voteCount > winningVoteCount) {
                winningVoteCount = candidates[i].voteCount;
                winningIndex = i;
            }
        }
        
        return candidates[winningIndex].name;
    }
}
```

![Screenshot 2025-05-19 134133](https://github.com/user-attachments/assets/02131372-a2e6-4334-89c6-6e6396689b26)

![Screenshot 2025-05-19 134210](https://github.com/user-attachments/assets/7c8e23a0-f20c-420a-8e07-c130e5b50024)



## que.2 Write a contract that manages a list of student records (name, roll number). Allow adding and retrieving student data.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract StudentRecords {
    struct Student {
        string name;
        uint rollNumber;
    }
    
    Student[] public students;
    mapping(uint => bool) public rollNumberExists;
    
    function addStudent(string memory _name, uint _rollNumber) public {
        require(!rollNumberExists[_rollNumber], "Roll number already exists");
        students.push(Student(_name, _rollNumber));
        rollNumberExists[_rollNumber] = true;
    }
    
    function getStudent(uint _index) public view returns (string memory, uint) {
        require(_index < students.length, "Invalid index");
        return (students[_index].name, students[_index].rollNumber);
    }
    
    function getStudentCount() public view returns (uint) {
        return students.length;
    }
}
```
![Screenshot 2025-05-19 134520](https://github.com/user-attachments/assets/ace41b36-6ffd-4ff8-8d9b-3da5ebbe7da9)

![image](https://github.com/user-attachments/assets/9a057d67-d522-4dcf-9f38-c618033704eb)



## que3 :- Develop a contract that only allows the deployer (owner) to call a specific function (use modifiers).

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract OwnerRestricted {
    address public owner;
    string private secretMessage;
    
    constructor() {
        owner = msg.sender;
    }
    
    modifier onlyOwner() {
        require(msg.sender == owner, "Only owner can call this");
        _;
    }
    
    function setSecretMessage(string memory _message) public onlyOwner {
        secretMessage = _message;
    }
    
    function getSecretMessage() public view onlyOwner returns (string memory) {
        return secretMessage;
    }
}
```

![Screenshot 2025-05-19 135013](https://github.com/user-attachments/assets/5d3437eb-5d7b-4dd8-9614-587d29866bf9)

![Screenshot 2025-05-19 135042](https://github.com/user-attachments/assets/b819da61-0951-4ad7-ac52-cb4e8f54f7e0)



## que4 :- Write a contract where people can donate Ether and the top 3 donors are tracked.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract TopDonors {
    struct Donor {
        address donorAddress;
        uint amount;
    }
    
    Donor[3] public topDonors;
    
    function donate() public payable {
        require(msg.value > 0, "Donation amount must be greater than 0");
        
        // Check if donor already exists in top 3
        for(uint i = 0; i < 3; i++) {
            if(topDonors[i].donorAddress == msg.sender) {
                topDonors[i].amount += msg.value;
                sortDonors();
                return;
            }
        }
        
        // If new donor has more than the smallest in top 3
        if(msg.value > topDonors[2].amount) {
            topDonors[2] = Donor(msg.sender, msg.value);
            sortDonors();
        }
    }
    
    function sortDonors() private {
        for(uint i = 1; i < 3; i++) {
            for(uint j = 0; j < i; j++) {
                if(topDonors[i].amount > topDonors[j].amount) {
                    Donor memory temp = topDonors[i];
                    topDonors[i] = topDonors[j];
                    topDonors[j] = temp;
                }
            }
        }
    }
}
```
![Screenshot 2025-05-19 135344](https://github.com/user-attachments/assets/43e0aff4-62e8-4806-b425-eb91509566ff)

![Screenshot 2025-05-19 135415](https://github.com/user-attachments/assets/8d0200ff-973c-44aa-85b2-03ab39740091)



## que5 :- Implement a simple auction system where users can place bids, and the highest bidder wins.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SimpleAuction {
    address public highestBidder;
    uint public highestBid;

    function bid() public payable {
        require(msg.value > highestBid, "Bid too low");

        if (highestBid != 0) {
            payable(highestBidder).transfer(highestBid);
        }

        highestBidder = msg.sender;
        highestBid = msg.value;
    }
}
```

![image](https://github.com/user-attachments/assets/bb48a800-72fe-4cdd-a474-f1de238e6f04)

![image](https://github.com/user-attachments/assets/18eb0b0f-91a3-466d-86c8-eeffb33f5382)



## que6 :- Create a contract that splits incoming Ether between 3 fixed addresses.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SplitEther {
    address payable public addr1;
    address payable public addr2;
    address payable public addr3;

    constructor(address payable _a1, address payable _a2, address payable _a3) {
        addr1 = _a1;
        addr2 = _a2;
        addr3 = _a3;
    }

    receive() external payable {
        uint share = msg.value / 3;
        addr1.transfer(share);
        addr2.transfer(share);
        addr3.transfer(msg.value - 2 * share); // handle remainder
    }
}
```

![Screenshot 2025-05-19 140939](https://github.com/user-attachments/assets/2d917e13-e4b2-43aa-80ca-bacc91b3cc09)

![image](https://github.com/user-attachments/assets/0a47efce-fe91-407a-9802-e4322051241d)

