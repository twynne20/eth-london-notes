# Fixing the max fee
  ```
  async fn premium_estimation(
      signer: Arc<DefaultSignerMiddleware>,
  ) -> Result<(ethers::types::U256, ethers::types::U256)> {
      // curly -- Hardcode the max_fee_per_gas value
      let max_fee_per_gas = ethers::types::U256::from(500_000_000u64); // 100 GWei
  
      let base_fee_per_gas = signer
          .get_block(ethers::types::BlockNumber::Latest)
          .await?
          .ok_or_else(|| anyhow!("Latest block not found"))?
          .base_fee_per_gas
          .ok_or_else(|| anyhow!("EIP-1559 not activated"))?;
  
      let fee_history = signer
          .fee_history(
              ethers::utils::EIP1559_FEE_ESTIMATION_PAST_BLOCKS,
              ethers::types::BlockNumber::Latest,
              &[ethers::utils::EIP1559_FEE_ESTIMATION_REWARD_PERCENTILE],
          )
          .await?;
      
      log::info!("Base fee per gas: {:?}", base_fee_per_gas);
      log::info!("Max fee per gas: {:?}", max_fee_per_gas);
  
      let max_priority_fee_per_gas = ethers::types::U256::from(500_000_000u64);
      // let max_priority_fee_per_gas = estimate_priority_fee(fee_history.reward); //overestimate?
      //curly
      // let potential_max_fee = base_fee_surged(base_fee_per_gas);
      // let max_fee_per_gas = if max_priority_fee_per_gas > potential_max_fee {
      //     max_priority_fee_per_gas + potential_max_fee
      // } else {
      //     potential_max_fee
      // };
  
      Ok((max_priority_fee_per_gas, max_fee_per_gas))
  }
  ```
