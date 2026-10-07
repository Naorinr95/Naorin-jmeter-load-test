# JMeter Load Test with GitHub Actions

A small Apache JMeter test plan that sends concurrent requests to the login endpoint of the public OrangeHRM demo site, run in a GitHub Actions pipeline that publishes the JMeter HTML dashboard on every push.

![Run JMeter Load Test](https://github.com/Naorinr95/Naorin-jmeter-load-test/actions/workflows/jmeter-test.yml/badge.svg?branch=main)

## What the test does

| Setting | Value |
|---------|-------|
| Target | `https://opensource-demo.orangehrmlive.com/web/index.php/auth/validate` (POST) |
| Virtual users | 10 on every push; configurable (the plan defaults to 100) |
| Ramp-up | 10 seconds |
| Loops | 1 |

The plan has one HTTP sampler with a header manager, plus a Results Tree and a Summary Report listener. The number of users is read from the `threads` property, so the same plan serves routine runs and larger stress runs.

## Sample result

From a CI run with 10 users:

| Metric | Value |
|--------|-------|
| Samples | 30 (10 users x 3 requests, including the redirect) |
| Errors | 0 |
| Mean response time | about 550 ms |
| Median response time | about 380 ms |
| 95th percentile | about 1.8 s |

## Files

```
OrangeHRM_Test.jmx          JMeter test plan
.github/workflows/          CI pipeline (jmeter-test.yml)
```

## Running locally

Install [Apache JMeter](https://jmeter.apache.org/download_jmeter.cgi) 5.6.3 or later, then:

```bash
jmeter -n -t OrangeHRM_Test.jmx -Jthreads=10 -l results.jtl -e -o html-report
```

JMeter needs `results.jtl` and `html-report/` to be new or empty, so delete them before running again. Open `html-report/index.html` for the dashboard.

## Continuous integration

On every push to `main`, GitHub Actions:

1. Installs Java 17 and Apache JMeter 5.6.3 (cached after the first run)
2. Runs the test plan in non-GUI mode with 10 users
3. Uploads `results.jtl` and the HTML dashboard as artifacts (even if the run fails)

To run a larger load, open the **Actions** tab, choose **Run JMeter Load Test**, click **Run workflow**, and enter the number of users. Then download `jmeter-html-report` from the run page.

## Notes

- The target is a public demo site that this project does not own, so routine pushes use a small load (10 users). Larger runs are started manually and only occasionally.
- The request sends no credentials, so it measures how the endpoint responds to concurrent requests (a redirect back to the login page), not an authenticated session.
- Generated results are git-ignored and are produced fresh on each run.

## Author

**Rifat Naorin**, Software QA Engineer

[GitHub](https://github.com/Naorinr95)
