# JMeter Load Test with GitHub Actions

A small Apache JMeter test plan that sends concurrent requests to the login endpoint of the public OrangeHRM demo site, run in a GitHub Actions pipeline that publishes the JMeter HTML dashboard on every push.

## What the test does

| Setting | Value |
|---------|-------|
| Target | `https://opensource-demo.orangehrmlive.com/web/index.php/auth/validate` (POST) |
| Virtual users | 10 |
| Ramp-up | 10 seconds |
| Loops | 1 |

The plan has one HTTP sampler with a header manager, plus a Results Tree and a Summary Report listener.

## Files

```
OrangeHRM_Test.jmx          JMeter test plan
.github/workflows/          CI pipeline (jmeter-test.yml)
```

## Running locally

Install [Apache JMeter](https://jmeter.apache.org/download_jmeter.cgi) 5.6.3 or later, then:

```bash
jmeter -n -t OrangeHRM_Test.jmx -l results.jtl -e -o html-report
```

JMeter needs `results.jtl` and `html-report/` to be new or empty, so delete them before running again. Open `html-report/index.html` for the dashboard.

## Continuous integration

On every push to `main` and on demand, GitHub Actions:

1. Installs Java 17 and Apache JMeter 5.6.3
2. Runs the test plan in non-GUI mode
3. Uploads `results.jtl` and the HTML dashboard as artifacts (even if the run fails)

Open the **Actions** tab, choose a run, and download `jmeter-html-report`.

## Notes

- The target is a public demo site that this project does not own, so the load is kept deliberately small. Please don't raise the user count against it.
- The request sends no credentials, so it measures how the endpoint responds under concurrent requests, not an authenticated session.
- Generated results are git-ignored and are produced fresh on each run.

## Author

**Rifat Naorin**, Software QA Engineer

[GitHub](https://github.com/Naorinr95)
