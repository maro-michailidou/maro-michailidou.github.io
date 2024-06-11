## Documentation for MATLAB Class GUI_get_all_positionsTest

### Overview
The `GUI_get_all_positionsTest` class is a set of unit tests designed to validate the `GUI_get_all_positions` function.  Using MATLAB a series of tests were created to ensure the correct response of the function to various input scenarios.

### Methods
#### Test Methods

##### testGUI_get_all_positions(testCase)
**Description:** Verifies that the `GUI_get_all_positions` function returns correct 3x1 real vectors for valid lengths and angles inputs.

**Implementation:**

```matlab
function testGUI_get_all_positions(testCase)
  % Define lengths and angles for the test
  lengths = [1, 2, 3];
  angles = [pi/4, pi/4, pi/4, pi/4];

  % Call the function to get joint positions
  [MCP_aa_fe_position, PIP_position, DIP_position, eff_position] = GUI_get_all_positions(lengths, angles);

  % Verify the size and real number properties of the outputs
  testCase.verifySize(MCP_aa_fe_position, [3, 1]);
  testCase.verifyTrue(isreal(MCP_aa_fe_position));

  testCase.verifySize(PIP_position, [3, 1]);
  testCase.verifyTrue(isreal(PIP_position));

  testCase.verifySize(DIP_position, [3, 1]);
  testCase.verifyTrue(isreal(DIP_position));

  testCase.verifySize(eff_position, [3, 1]);
  testCase.verifyTrue(isreal(eff_position));
end
```

**Details:**
- Defines valid lengths and angles for the test.
- Calls `GUI_get_all_positions` to obtain joint positions.
- Uses `testCase` methods to verify that:
    - Each output position is a 3x1 vector.
    - Each output position contains only real numbers.

##### testInvalidLengthsInput(testCase)
**Description:** Checks if the `GUI_get_all_positions` function handles invalid lengths input like an empty array.

**Implementation:**

```matlab
function testInvalidLengthsInput(testCase)
  % Define empty array for lengths (invalid input)
  lengths = [];
  angles = [pi/4, pi/4, pi/4, pi/4];

  % Verify the function throws an error for invalid lengths
  testCase.verifyError(@() GUI_get_all_positions(lengths, angles), 'MATLAB:badsubscript');
end
```

**Details:**
- Defines an empty array for lengths (invalid input).
- Uses `testCase.verifyError` to ensure the function throws a 'MATLAB:badsubscript' error for this input.
- This verifies that the function handles incorrect indexing or subscript usage within the code.
- In this case, it ensures that the function behaves as expected when given invalid subscript inputs,

##### testInvalidAnglesInput(testCase)
**Description:** Checks if the `GUI_get_all_positions` function handles invalid angles input like an incorrect size array.

**Implementation:**

```matlab
function testInvalidAnglesInput(testCase)
  % Define valid lengths and invalid angles (incorrect size)
  lengths = [1, 2, 3];
  angles = [pi/4, pi/4];  % Incorrect size

  % Verify the function throws an error for invalid angles
  testCase.verifyError(@() GUI_get_all_positions(lengths, angles), 'MATLAB:badsubscript');
end
```

**Details:**
- Defines valid lengths and an array of incorrect size for angles (invalid input).
- Uses `testCase.verifyError` to ensure the function throws a 'MATLAB:badsubscript' error for this input.
- The `testCase.verifyError` method allows you to check if a specific error is thrown when executing a part of your code within the test.

##### testInvalidForwardKinematicsInput(testCase)
**Description:** Checks the behavior of the `forward_kinematics_func` function with invalid angles input like an incorrect size array.

**Implementation:**

```matlab
function testInvalidForwardKinematicsInput(testCase)
  % Define invalid angles (incorrect size) and valid lengths
  angles = [0, 0];  % Incorrect size
  L1 = 5;
  L2 = 4;
  L3 = 3;
  theta_eff = 0;

  % Verify the function throws an error for invalid angles
  testCase.verifyError(@() forward_kinematics_func(angles, L1, L2, L3, theta_eff), 'MATLAB:badsubscript');
end
```

**Details:**
- Defines an array of incorrect size for angles (invalid input) and valid lengths.
- Uses `testCase.verifyError` to ensure the `forward_kinematics_func`

## Documentation for MATLAB Class TestForwardKinematics

### Overview
The `TestForwardKinematics` class is a set of unit tests designed to validate two functions:
  - `get_forward_transformation_matrix`
  - `forward_kinematics_func`

### Methods
#### Test Methods

##### testGetForwardTransformationMatrixWithDifferentAngles(testCase)
**Description:** Verifies the output of `get_forward_transformation_matrix` for different input angles.

**Implementation:**

```matlab
function testGetForwardTransformationMatrixWithDifferentAngles(testCase)
  % Call the function to get the transformation matrix
  T_simplify = get_forward_transformation_matrix();

  % Verify the size and last row of the output matrix
  testCase.verifySize(T_simplify, [4, 4]);
  testCase.verifyEqual(T_simplify(4, :), sym([0, 0, 0, 1]));
end
```

**Details:**
- Calls `get_forward_transformation_matrix` without arguments.
- Verifies the output is a 4x4 matrix.
- Ensures the last row is [0, 0, 0, 1] using `testCase.verifyEqual`.

##### testForwardKinematicsFuncWithZeroAngles(testCase)
**Description:** Verifies the output of `forward_kinematics_func` for zero joint angles.

**Implementation:**

```matlab
function testForwardKinematicsFuncWithZeroAngles(testCase)
  % Define zero angles and segment lengths
  angles = [0, 0, 0];
  L1 = 5;
  L2 = 4;
  L3 = 3;
  theta_eff = 0;

  % Call the function to get the end-effector position
  position = forward_kinematics_func(angles, L1, L2, L3, theta_eff);

  % Verify the size of the output vector
  testCase.verifySize(position, [3, 1]);

  % Calculate expected position for zero angles
  expectedPosition = [L1 + L2 + L3; 0; 0];

  % Verify the output matches the expected position with tolerance
  testCase.verifyEqual(position, expectedPosition, 'AbsTol', 1e-6);
end
```

**Details:**
- Defines zero angles and specific segment lengths for the test.
- Calls `forward_kinematics_func` with these inputs.
- Verifies the output is a 3x1 vector.
- Calculates the expected end effector position for zero angles.
- Uses `testCase.verifyEqual` to ensure the actual position matches the expected position within a tolerance of 1e-6.

##### testForwardKinematicsFuncWithNonZeroAngles(testCase)
**Description:** Verifies the output of `forward_kinematics_func` for non-zero joint angles.

**Implementation:**

```matlab
function testForwardKinematicsFuncWithNonZeroAngles(testCase)
  % Define non-zero angles and segment lengths
  angles = [pi/4, pi/4, pi/4];
  L1 = 5;
  L2 = 4;
  L3 = 3;
  theta_eff = pi/2;

  % Call the function to get the end-effector position
  position = forward_kinematics_func(angles, L1, L2, L3, theta_eff);

  % Verify the size of the output vector
  testCase.verifySize(position, [3, 1]);

  % Calculate expected position for non-zero angles (based on manipulator kinematics)
  expectedPosition = [L1*cos(angles(2)) + L2*cos(angles(2) + angles(3)) + L3*cos(theta_eff); 
                      L1*sin(angles(2)) + L2*sin(angles(2) + angles(3)) + L3*sin(theta_eff); 
                      0];

  % Verify the output matches the expected position with tolerance
  testCase.verifyEqual(position, expectedPosition, 'AbsTol', 1e-6);
end
```

**Details:**
- Defines non-zero angles and specific segment lengths for the test.
- Calls `forward_kinematics_func` with these inputs.
- Verifies the output is a 3x1 vector.
- Calculates the expected end-effector position for the given angles using forward kinematics formulas.

## Documentation for MATLAB Class TestRST_4DOF_finger_model

### Overview
The `TestRST_4DOF_finger_model` class is a set of unit tests designed to validate the functionalities of the `RST_4DOF_finger_model` class. 

### Properties
- `fingerModel`: Stores an instance of the `RST_4DOF_finger_model` class created during test setup.

### Methods
#### Setup Method
- `createFingerModel`: This method is called before each test and creates a new `RST_4DOF_finger_model` instance with specific parameters (0.05 for all finger segment lengths and a mass of 100). This instance is stored in the `fingerModel` property for use in subsequent tests.

#### Test Methods

##### testConstructor(testCase)
**Description:** Verifies that the `RST_4DOF_finger_model` constructor initializes properties correctly.

**Implementation:**

```matlab
function testConstructor(testCase)
  % Access properties from the created fingerModel object
  fingerProps = testCase.fingerModel;
  
  % Verify the values of finger segment parameters (P_ph, M_ph, D_ph)
  testCase.verifyEqual(fingerProps.P_ph, 0.05);
  testCase.verifyEqual(fingerProps.M_ph, 0.05);
  testCase.verifyEqual(fingerProps.D_ph, 0.05);
  
  % Verify the class types of fingerRobotModel and ikSolver properties
  testCase.verifyInstanceOf(fingerProps.fingerRobotModel, 'rigidBodyTree');
  testCase.verifyInstanceOf(fingerProps.ikSolver, 'generalizedInverseKinematics');
end
```

**Details:**
- Retrieves the `fingerModel` properties (`P_ph`, `M_ph`, `D_ph`, `fingerRobotModel`, and `ikSolver`).
- Uses `testCase` methods to verify:
    - Finger segment parameter values (`P_ph`, `M_ph`, and `D_ph`) are 0.05 each.
    - `fingerRobotModel` is of class `rigidBodyTree`.
    - `ikSolver` is of class `generalizedInverseKinematics`.

##### testGetFingerEEPosition(testCase)
**Description:** Verifies that the `getFingerEEPosition` method returns the correct end-effector position.

**Implementation:**

```matlab
function testGetFingerEEPosition(testCase)
  % Define a new desired EE position
  newPosition = [0.2, 0.2, 0.2];

  % Move the finger using moveFingerToPosition (assuming it updates internal state)
  testCase.fingerModel.moveFingerToPosition(newPosition, false);

  % Get the EE position using getFingerEEPosition
  actualPosition = testCase.fingerModel.getFingerEEPosition();

  % Verify that the actual position matches the desired position within tolerance
  testCase.verifyEqual(actualPosition, newPosition, 'AbsTol', 1e-6);
end
```

**Details:**
- Defines a new desired EE position.
- Calls `moveFingerToPosition` to move the finger.
- Calls `getFingerEEPosition` to get the current EE position.
- Uses `testCase.verifyEqual` to ensure the retrieved position matches the desired position with a tolerance of 1e-6.

##### testGetFingerAngles(testCase)
**Description:** Verifies that the `getFingerAngles` method returns the zero angles for a newly created finger model.

**Implementation:**

```matlab
function testGetFingerAngles(testCase)
  % Get the finger joint angles using getFingerAngles
  angles = testCase.fingerModel.getFingerAngles();

  % Verify that all angles are zero
  testCase.verifyEqual(angles, zeros(4, 1));
end
```

**Details:**
- Calls `getFingerAngles` to retrieve the current finger joint angles.
- Uses `testCase.verifyEqual` to ensure all angles in the returned vector are zero.

##### testMoveFingerToPosition(testCase)
**Description:** Verifies that the `moveFingerToPosition` method moves the finger to the desired position.

**Implementation:**

```matlab
function testMoveFingerToPosition(testCase)
  % Define a desired position to move the finger
  targetPosition = [0.1, 0.05, 0];

  % Move the finger to the target position using moveFingerToPosition
  testCase.fingerModel.moveFingerToPosition(targetPosition, false);
  ```

 ## Documentation for MATLAB Class tests_MQTT_finger_sender

### Overview
The `tests_MQTT_finger_sender` class is a set of unit tests designed to validate functionalities of the `MQTT_finger_sender` class. 

### Test Methods

##### testSendDataWithInvalidEndEffectorPosition(testCase)

**Description:** Verifies that `sendData` throws an error for an invalid `endEffectorPosition` input like NaN.

**Implementation:**

```matlab
function testSendDataWithInvalidEndEffectorPosition(testCase)
  % Create an MQTT finger sender object
  sender = MQTT_finger_sender();

  % Define invalid end effector position (NaN) and other inputs
  endEffectorPosition = NaN;
  jointAngles = [pi/4, pi/4, pi/4];
  pauseSeconds = 1;

  % Call sendData with invalid input and expect an error
  try
    sender.sendData(endEffectorPosition, jointAngles, pauseSeconds);
    testCase.fail('Expected an error for invalid endEffectorPosition.');
  catch ex
    % Verify the error is related to data validation (MATLAB:invalidType)
    testCase.verifyNotEmpty(ex.identifier);
    testCase.assertTrue(contains(ex.identifier, 'MATLAB:invalidType'));
  end
end
```

**Details:**
- Creates an `MQTT_finger_sender` object.
- Defines an invalid `endEffectorPosition` such as NaN and other inputs for `sendData`.
- Attempts to call `sendData` and uses a `try-catch` to handle potential errors.
- If no error is thrown, the test fails using `testCase.fail`.
- If an error is thrown, the test verifies that the error identifier is related to a data type validation like`MATLAB:invalidType`.

##### testSendDataBatch(testCase)

**Description:** Verifies that `sendDataBatch` sends a single data point without warnings.

**Implementation:**

```matlab
function testSendDataBatch(testCase)
  % Create an MQTT finger sender object
  sender = MQTT_finger_sender();

  % Define data for a single point
  t = 1;
  endEffectorPosition = [1, 2, 3];
  jointAngles = [pi/4, pi/4, pi/4];

  % Add data to the sender's batch
  sender.addToDataBatch(t, endEffectorPosition, jointAngles);

  % Call sendDataBatch and verify no warnings are generated
  testCase.verifyWarningFree(@() sender.sendDataBatch());
end
```

**Details:**
- Creates an `MQTT_finger_sender` object.
- Defines data for a single time point (`t`, `endEffectorPosition`, `jointAngles`).
- Calls `addToDataBatch` to add the data point to the sender's internal batch.
- Uses `testCase.verifyWarningFree` to ensure no warnings are generated when calling `sendDataBatch`.

##### testConstructor(testCase)

**Description:** Verifies that the constructor initializes class properties correctly.

**Implementation:**

```matlab
function testConstructor(testCase)
  % Create an MQTT finger sender object
  sender = MQTT_finger_sender();

  % Verify the values of various class properties
  testCase.verifyEqual(sender.clientID, "BME_TEAM_A");
  testCase.verifyEqual(sender.brokerAddresses_OverUnTCP, [...
      "tcp://broker.hivemq.com", "tcp://mqtt3.thingspeak.com", ...
      "tcp://mqtt.flespi.io"]);
  testCase.verifyEqual(sender.port_OverUnTCP, 1883);
  testCase.verifyEqual(sender.topic, "BME12_finger_configuration");
  testCase.verifyNotEmpty(sender.mqClient);  % Non-empty client object
  testCase.verifyNotEmpty(sender.dataBatch);  % Non-empty data batch
end
```

**Details:**
- Creates an `MQTT_finger_sender` object.
- Uses `testCase` methods to verify the values of various properties like:
    - `clientID`
    - `brokerAddresses_OverUnTCP` (list of broker addresses)
    - `port_OverUnTCP` (port number)
    - `topic` (MQTT topic)
    - `mqClient` (non-empty MQTT client object)
    - `dataBatch` (non-empty data batch)

##### testSendDataWithInvalidJointAngles(testCase)

**Description:** Verifies that `sendData` handles invalid `jointAngles` input string without throwing an error. 
 

## Documentation for MATLAB Class custom_objective_funcTest_renamed

### Overview
The `custom_objective_funcTest_renamed` class is a set of unit tests designed to validate the `custom_objective_func` function. It ensures the function calculates the position error for a 3-link manipulator given a desired and an actual end effector positions.

### Test Methods

##### testCustomObjectiveFunc(testCase)

**Description:** Verifies the behavior of `custom_objective_func` for different desired end effector positions.

**Implementation:**

```matlab
function testCustomObjectiveFunc(testCase)
  % Define manipulator parameters (link lengths)
  L1 = 5;
  L2 = 4;
  L3 = 3;
  angles = [0, 0, 0];  % Assume initial joint angles are zero

  % Test Case 1: Zero desired position
  x_des = L1 + L2 + L3;
  y_des = 0;
  z_des = 0;
  theta_eff = 0;

  % Calculate error using custom_objective_func
  error = custom_objective_func(angles, L1, L2, L3, x_des, y_des, z_des, theta_eff);

  % Verify the size of the output error (3x1 vector) and zero error
  testCase.verifySize(error, [3, 1]);
  expectedError = [0; 0; 0];
  testCase.verifyEqual(error, expectedError, 'AbsTol', 1e-6);
  disp('Test 1 passed: Zero error for zero desired position');

  % Test Case 2: Non-zero desired position
  x_des = 2;
  y_des = 1;
  z_des = 0;
  theta_eff = 0;

  % Calculate error using custom_objective_func
  error = custom_objective_func(angles, L1, L2, L3, x_des, y_des, z_des, theta_eff);

  % Verify that the error is not zero (indicating a difference)
  testCase.verifyNotEqual(error, expectedError);
  disp('Test 2 passed: Non-zero error for non-zero desired position');
end
```

**Details:**
- Defines link lengths (`L1`, `L2`, `L3`) and initial joint angles (`angles`) for the manipulator.
- **Test Case 1:**
    - Sets the desired end effector position to `[L1 + L2 + L3, 0, 0]`, which coincides with the zero-angle position.
    - Calls `custom_objective_func` to calculate the position error.
    - Verifies the output error is a 3x1 vector and all elements are zero using `testCase.verifySize` and `testCase.verifyEqual`.
- **Test Case 2:**
    - Sets the desired end effector position to a non-zero value `[2, 1, 0]`.
    - Calls `custom_objective_func` to calculate the position error.
    - Verifies the output error is not equal to the zero error from Test Case 1 using `testCase.verifyNotEqual`.
