# Homework - Basic Auth API Implementation

#### Key Requirement
  * create user
  * delete user
  * create role
  * delete role
  * add a role to a user
  * authenticate
  * invalidate
  * check role by token
  * list all role by token

#### Instruction

  * Download file TestAuth2.jar into your local machine.
  * Import TestAuth2.jar into your Java library as an dependency.
  * Test out the API by creating an AuthAPI instance. You need to pass a Clock instance.

  ```java
     AuthAPI authAPI = new AuthAPI(Clock.systemDefaultZone());
  ```
#### High Level Design Solution
  1. Design 3 Object classes
  
      User
      
        User class with a string field called name as key, and set of token associated with this user, set of roles associated with this user
      
      Role
      
        Role class with a string field called name as a key.
      
      Token
      
        Token class with a string field as a key, a Instant field to mark when the token is created by user.
 
 2. Use 3 HashMap in the AuthAPI to represent the relationship between User, Role and Token.
    
    1. userMap is the only source of truth for all available users in current AuthAPI instance.
    2. roleMap is the only source of truth that all available roles int current AuthAPI instance. RoleMap represent the relationship between users and role. The key of this HashMap is RoleName, and the value is all users has that role associated with.
    3. tokenMap represents the relationship between users and token. This HashMap should be a 1-1 mapping. Each token can only point to one user.
    
 3. Key functions explanation
    
    1. public function: deleteRole
       
            For each deleteRole operation, we need to handle two relationship. The first is to find all users assoicated with target role, remove role from the list for each user. 
            The second step is to remove the role from roleMap.
    2. public function: authenticate
        
            1. Check the user has a correct input password
            2. Keep try to generate a proper random key with current timestamp as createdTime for given user
            3. Update tokenMap and User's token list
            
    3. public function: invalidate
    
            1. Check if the token is assoicated with any user by checking tokenMap
            2. Update tokenMap and User's token list
            
    4. private function: getUserByToken
            
            There are serveral steps:
                1. Validate if the token is a valid token by checking tokenMap
                2. Find target user by tokenMap
                3. From user's token list, find target token to verify if the created time is older than the pre-configuraed duration.
                        if the token expire, we should invalidate the key
                4. If it meet all the conditions, return the target user.
 
 #### Optimization in future
 
 1. Unit tests with PowerMock clock
 
    Current implementation ask user to pass a Clock instance since I plan to mock the Clock class in unit test. But due 
    to limited knowledge and time, mocking does not run successfully in the unit tests. 
    
 2. Token generation
 
     Current token generation will first check if a token already in the tokenMap. In the future, we can update generation 
     algorithm to associated with User Id. In this case, we can only check if the given token is duplicated in a given user's token list.
    
 3. Encode Algorithm
 
    Currently, we just use Java basic Base64 Encoder. We could try other encode function to get a better security.

    
  

