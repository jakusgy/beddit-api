# User account related resources

## POST /api/v1/user/password_reset

Request password reset for user. The user will be sent an email which includes
instructions for setting a new password.

**Authentication**

Request must be authenticated by Application specific token.

**Request**

```javascript
{
  "email" : "example@email.com"
}
```

**Response**


```javascript
{
  "email" : "example@email.com"
}
```

**Errors**

Error identifier | HTTP Status | Description
-----------------|-------------|------------
no_such_user | 400 | User account with that email was not found.
email_send_failed | 500 | Password reset email could not be sent. This can happen due to invalid email address, or temporary problem with email sending service.


# Sleep data resources

## Sleep data format

A "Sleep" object represents the sleep measurements for a single night. This
format is used in several resources in the API. It is later referred to as
\<SLEEP DATA\>.

An example of a sleep object is shown below. Note that it may not always include
all of the fields. A field may be missing if it was not possible to compute it.

```javascript
{
  "date" : "2012-05-30",
  "timezone" : "Europe/Helsinki",
  "start_timestamp" : 1371472503.646541,
  "end_timestamp" : 1371492826.623422,
  "session_range_start" : 1371472503.646541,
  "session_range_end" : 1371492826.623422,
  "properties" : {
    "score_bed_exits" : 2,
    "score_amount_of_sleep" : 99,
    "score_snoring" : 10.3,
    "score_sleep_latency" : 5,
    "score_sleep_efficiency" : -20,
    "score_awakenings" : 1
  },
  "time_value_tracks" : {
    "alarm_event" : {
      "items" : [
        [1393252920.32,  0],
        [1393252926.514, 2]
      ],
      "value_data_type" : "float32"
    },
    "sleep_stages" : {
      "items" : [
        [1393249545.041, 65],
        [1393249556.041, 65]
      ],
      "value_data_type" : "int16"
    },
    "snoring_episodes" : {
      "items" : [],
      "value_data_type" : "float32"
    },
    "presence" : {
      "items" : [
        [1371472503.646541, 0]
      ],
      "value_data_type" : "float32"
    }
  },

  // Only present in responses from server:
  "updated" : 1371482503.646541
}
```

The fields of the sleep object are described below.

### Timezone

The name of the timezone where the measurement was made. List of time zone names
can be found here: http://en.wikipedia.org/wiki/List_of_tz_database_time_zones

### Properties

Scalar properties that belong to the sleep object.

Property | Meaning
---------|--------
sleep_time_target | The user's personal sleep time target in seconds
resting_heart_rate | The resting heart rate reading for the night
average_respiration_rate| The average respiration rate reading over the night
sleep_latency | The time it takes to fall asleep, in seconds. If the subject did not fall asleep, the value is missing.
away_episode_count | Number of absences during the night
total_snoring_episode_duration | Total amount of snoring, in seconds
stage_duration_A | Total time in away state in the measurement period, in seconds
stage_duration_S | Total time in sleep state in the measurement period
stage_duration_W | Total time in wake state in the measurement period
stage_duration_G | Total amount of "missing signal" time in the measurement period
score_bed_exits | Sleep score item for number of bed exits
score_amount_of_sleep | Sleep score item for total amount of sleep
score_snoring | Sleep score item for total duration of snoring episodes
score_sleep_latency | Sleep score item for time it took to fall asleep
score_sleep_efficiency | Sleep score item for sleep time vs time spent in bed
score_awakenings | Sleep score item for number of awakenings during the night

### Time-value tracks

A time-value track in a Sleep object is a sequence of timestamp-value pairs,
where the timestamp is a Unix timestamp (seconds since Jan 1, 1970).
The time-value tracks are listed below.

#### Alarm event

List of alarm clock time and event type values.

Value | Meaning
------|--------
0 | Alarm started ringing
1 | User snoozed alarm
2 | User dismissed alarm
3 | Alarm was ignored (stopped automatically after x minutes)
4 | Alarm was automatically dismissed after user had ignored it several times

#### Sleep stages

Value data type: **uint8**.

This is the state transition sequence for the sleep stages. The timestamp is the
time when a state begins and the value represents the new state. The gap
represents a period of missing signal in the measurement period.

Value | Meaning
------|--------
65 | Away from bed
83 | Asleep
87 | Awake
71 | Gap in measurement

#### Snoring episodes

value data type: **float32**

The timestamp represents the time of a snoring episode and the value is the
length of the episode. The snoring episodes give a high-level view of snoring
activity and have around 10-minute resolution.

#### Sleep cycles

Value data type: **float32**

The timestamp and value represents the sleep depth at that time. The value is a
floating point number between 0.0 and 1.0. Value 0.0 corresponds to lightest
possible sleep and 1.0 to deepest possible sleep. There will be one datapoint
every 2 minutes, beginning from the moment the subject is deemed to be sleeping
and ending when the subject ultimately wakes up.

#### Heart rate curve

Value data type: **float32**

The timestamp and value pairs describe a smooth heart rate curve. The time
between curve data points is around 5 minutes. A gap in the curve is marked by a
negative value. A good way to display the curve is to split the time-value
sequence into curve parts by the negative gap-values and display each curve part
individually.

### Updated

The updated field contains the timestamp of when the sleep object was put to the
server. If you want to periodically query new sleep objects, you can store the
latest updated value, and use it in query.


## GET /api/v1/user/:user_id/sleep

**Authentication**

Request must be authenticated by User specific token.

**Query parameters**

The following parameters for querying sleeps are available:

Name           | Value
---------------|----------------------------------------------
start_date     | Start date of range, e.g. "2014-01-01"
end_date       | End date of range, e.g. "2014-01-02"
updated_after  | Time of last update, e.g. 1371482503.646541

When querying you must either use the `updated_after` or both `start_date` and
`end_date`.

**Response**

```javascript
[
  <SLEEP DATA>,
  <SLEEP DATA>,
  ...
]
```
