# Control Validator

The `control_validator` is a module that checks the validity of the output of the control component. The status of the validation can be viewed in the `/diagnostics` topic.

![control_validator](./image/control_validator.drawio.svg)

## Supported features

The following features are supported for the validation and can have thresholds set by parameters.
The listed features below does not always correspond to the latest implementation.

| Description                                                                        | Arguments                                                                              |                Diagnostic equation                | Implemented function name       |
| ---------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- | :-----------------------------------------------: | ------------------------------- |
| Inverse velocity: Measured velocity has a different sign from the target velocity. | measured velocity $v$, target velocity $\hat{v}$, and threshold velocity parameter $k$ |    $v \hat{v} < 0, \quad \lvert v \rvert > k$     | `checkValidVelocityDeviation()` |
| Overspeed: Measured speed exceeds target speed significantly.                      | measured velocity $v$, target velocity $\hat{v}$, and threshold ratio parameter $r$    | $\lvert v \rvert > (1 + r) \lvert \hat{v} \rvert$ | `checkValidVelocityDeviation()` |

- **Deviation check between reference trajectory and predicted trajectory** : invalid when the largest deviation between the predicted trajectory and reference trajectory is greater than the given threshold.

![trajectory_deviation](./image/trajectory_deviation.drawio.svg)

## Inputs/Outputs

### Inputs

The `control_validator` takes in the following inputs:

| Name                           | Type                              | Description                                                                    |
| ------------------------------ | --------------------------------- | ------------------------------------------------------------------------------ |
| `~/input/kinematics`           | nav_msgs/Odometry                 | ego pose and twist                                                             |
| `~/input/reference_trajectory` | autoware_planning_msgs/Trajectory | reference trajectory which is outputted from planning module to to be followed |
| `~/input/predicted_trajectory` | autoware_planning_msgs/Trajectory | predicted trajectory which is outputted from control module                    |

### Outputs

It outputs the following:

| Name                         | Type                                     | Description                                                               |
| ---------------------------- | ---------------------------------------- | ------------------------------------------------------------------------- |
| `~/output/validation_status` | control_validator/ControlValidatorStatus | validator status to inform the reason why the trajectory is valid/invalid |
| `/diagnostics`               | diagnostic_msgs/DiagnosticStatus         | diagnostics to report errors                                              |

## Parameters

The following parameters can be set for the `control_validator`:

### System parameters

| Name                         | Type | Description                                                                                                                                                                                                                                | Default value |
| :--------------------------- | :--- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------ |
| `publish_diag`               | bool | if true, diagnostics msg is published.                                                                                                                                                                                                     | true          |
| `diag_error_count_threshold` | int  | the Diag will be set to ERROR when the number of consecutive invalid trajectory exceeds this threshold. (For example, threshold = 1 means, even if the trajectory is invalid, the Diag will not be ERROR if the next trajectory is valid.) | true          |
| `display_on_terminal`        | bool | show error msg on terminal                                                                                                                                                                                                                 | true          |

### Algorithm parameters

#### Thresholds

The input trajectory is detected as invalid if the index exceeds the following thresholds.

| Name                                 | Type   | Description                                                                                                 | Default value |
| :----------------------------------- | :----- | :---------------------------------------------------------------------------------------------------------- | :------------ |
| `thresholds.max_distance_deviation`  | double | invalid threshold of the max distance deviation between the predicted path and the reference trajectory [m] | 1.0           |
| `thresholds.max_reverse_velocity`    | double | threshold velocity to valid the vehicle velocity [m/s]                                                      | WIP           |
| `thresholds.max_over_velocity_ratio` | double | threshold ratio to valid the vehicle velocity [*]                                                           | WIP           |
