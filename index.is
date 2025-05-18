// index.js
const express = require("express");
const axios = require("axios");
const cors = require("cors");
require("dotenv").config();

const app = express();
app.use(cors());
app.use(express.json());

const PAYSTACK_SECRET = process.env.PAYSTACK_SECRET_KEY;
const headers = {
  Authorization: `Bearer ${PAYSTACK_SECRET}`,
  "Content-Type": "application/json"
};

app.post("/send-gift", async (req, res) => {
  const { account_number, bank_code } = req.body;

  try {
    // Step 1: Resolve the account
    const resolveRes = await axios.get("https://api.paystack.co/bank/resolve", {
      headers,
      params: {
        account_number,
        bank_code
      }
    });

    const account_name = resolveRes.data.data.account_name;

    // Step 2: Create transfer recipient
    const recipientRes = await axios.post("https://api.paystack.co/transferrecipient", {
      type: "nuban",
      name: account_name,
      account_number,
      bank_code,
      currency: "NGN"
    }, { headers });

    const recipient_code = recipientRes.data.data.recipient_code;

    // Step 3: Initiate transfer
    await axios.post("https://api.paystack.co/transfer", {
      source: "balance",
      amount: 200000, // 2000 NGN in kobo
      recipient: recipient_code,
      reason: "Birthday gift for Ashvee 🎉"
    }, { headers });

    res.json({ success: true, message: "Transfer successful" });
  } catch (error) {
    console.error(error.response?.data || error.message);
    res.status(500).json({ success: false, error: "Transfer failed" });
  }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`🚀 Server running on http://localhost:${PORT}`);
});
