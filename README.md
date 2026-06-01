# Hosting Web Apps Directly on Databricks

Published: 2025-09-18
Medium: [https://medium.com/@kyle-t-jones/hosting-web-apps-directly-on-databricks-e932b7400c0c](https://medium.com/@kyle-t-jones/hosting-web-apps-directly-on-databricks-e932b7400c0c)

## Business context

Three-tier architecture works because it separates concerns. The presentation tier runs the interface. The application tier manages logic. The data tier handles persistence. In the past, these tiers lived far apart. Applications ran on servers in one place. Data lived in another. Integration was brittle and often insecure.

Databricks changes this dynamic. It not only delivers the strongest possible data tier, but now also lets you host applications directly on the platform. This collapses distance between the tiers. It reduces latency. It improves governance. It simplifies the development of data-driven applications.

The presentation tier can still be external, but the application tier now runs in the same secure environment as the data. That means no raw data leaves Databricks.



## Disclaimer

Educational/demo code only. Not financial, safety, or engineering advice. Use at your own risk. Verify results independently before any production or operational use.

## License

MIT — see [LICENSE](LICENSE).