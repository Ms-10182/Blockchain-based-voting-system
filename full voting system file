// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract VotingSystem {
    address public owner;
    bool public votingStarted;
    bool public votingEnded;

    struct Candidate {
        string name;
        address candidateAddress;
        uint256 voteCount;
    }

    struct Voter {
        string name;
        bool hasVoted;
        address votedFor;
    }

    mapping(address => Candidate) public candidates;
    mapping(address => Voter) public voters;
    address[] public candidateAddresses;

    modifier onlyOwner() {
        require(msg.sender == owner, "Only the contract owner can perform this action.");
        _;
    }

    modifier votingNotStarted() {
        require(!votingStarted, "Voting has already started.");
        _;
    }

    modifier votingStartedOnly() {
        require(votingStarted, "Voting has not started yet.");
        _;
    }

    modifier votingNotEnded() {
        require(!votingEnded, "Voting has already ended.");
        _;
    }

    constructor() {
        owner = msg.sender;
    }

    function startVoting(string memory _ownerName, address _ownerAddress) public votingNotStarted onlyOwner {
        votingStarted = true;
        voters[_ownerAddress].name = _ownerName;
        voters[_ownerAddress].hasVoted = false;
    }

    function addCandidate(string memory _name, address _candidateAddress) public onlyOwner votingNotStarted {
        require(candidateAddresses.length < 3, "Cannot add more than 3 candidates.");
        candidates[_candidateAddress] = Candidate(_name, _candidateAddress, 0);
        candidateAddresses.push(_candidateAddress);
    }

    function vote(address _candidateAddress) public votingStartedOnly votingNotEnded {
        require(!voters[msg.sender].hasVoted, "You have already voted.");
        require(candidates[_candidateAddress].candidateAddress != address(0), "Invalid candidate address.");

        voters[msg.sender].hasVoted = true;
        voters[msg.sender].votedFor = _candidateAddress;
        candidates[_candidateAddress].voteCount++;
    }

    function endVoting() public votingStartedOnly onlyOwner {
        votingEnded = true;
    }

    function getCandidates() public view returns (address[] memory) {
        return candidateAddresses;
    }

    function getCandidateDetails(address _candidateAddress)
        public
        view
        returns (string memory name, uint256 voteCount)
    {
        require(votingEnded, "Voting is still ongoing.");

        name = candidates[_candidateAddress].name;
        voteCount = candidates[_candidateAddress].voteCount;
    }

    function getVoterDetails(address _voterAddress)
        public
        view
        returns (string memory name, bool hasVoted, address votedFor)
    {
        return (
            voters[_voterAddress].name,
            voters[_voterAddress].hasVoted,
            voters[_voterAddress].votedFor
        );
    }

    function getWinners() public view returns (string[] memory winners) {
        require(votingEnded, "Voting is still ongoing.");

        uint256 highestVoteCount = 0;

        // Loop through all candidates to find the highest vote count
        for (uint256 i = 0; i < candidateAddresses.length; i++) {
            address candidateAddress = candidateAddresses[i];
            uint256 candidateVoteCount = candidates[candidateAddress].voteCount;

            if (candidateVoteCount > highestVoteCount) {
                highestVoteCount = candidateVoteCount;
            }
        }

        // Loop through candidates again to find the winners
        string[] memory winnerNames = new string[](candidateAddresses.length);
        uint256 winnerCount = 0;

        for (uint256 i = 0; i < candidateAddresses.length; i++) {
            address candidateAddress = candidateAddresses[i];
            uint256 candidateVoteCount = candidates[candidateAddress].voteCount;
            string memory candidateName = candidates[candidateAddress].name;

            if (candidateVoteCount == highestVoteCount) {
                winnerNames[winnerCount] = candidateName;
                winnerCount++;
            }
        }

        // Resize the winners array to match the actual number of winners
        winners = new string[](winnerCount);
        for (uint256 i = 0; i < winnerCount; i++) {
            winners[i] = winnerNames[i];
        }
    }
}
