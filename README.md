This repo was branched from https://github.com/google-research/google-research/tree/master/al_for_fep

The active learning cycle is defined by a single configuration file. Simply pass
the configuration to the module to run. It is possible to override configuration
parameters on the command line (via ConfigDict flags).

This is the expected format of the configuration with short descriptions for each
field:

```
ml_collections.ConfigDict({
      'model_config':
          ml_collections.ConfigDict({
              'model_type': String. The identifier for the model to use. See the
                              models/ subfolder for currently supported models.
              'hyperparameters': ml_collections.ConfigDict() holding extra 
                                parameters for the model (e.g. l1_ratio for 
                                sklearn's ElasticNet)
              'tuning_hyperparameters': Dictionary. If the model performs 
                                        non-native hyperparameter tuning, the 
                                        search space is specified here.
              'features':
                  ml_collections.ConfigDict({
                      'feature_type': String. Specifier for parsing features from 
                                      csv input.
                      'params': {
                          'feature_column': String. Identifies the csv column 
                                            holding the information needed for 
                                            feature calculation (usually SMILES).
                          'fingerprint_size': Integer. Number of bits to use for 
                                              Morgan fingerprints.
                          'fingerprint_radius': Integer. Radius to use for Morgan 
                                                fingerprints.
                      }
                  }),
              'targets':
                  ml_collections.ConfigDict({
                      'feature_type': String. Specifies the type of the target 
                                      (typically 'number').
                      'params': {
                          'feature_column': String. Name of csv column holding the 
                                            target information. No post-parsing 
                                            processing is done.
                      }
                  })
          }),
      'selection_config':
          ml_collections.ConfigDict({
              'selection_type':
                  'greedy',
              'hyperparameters': ml_collections.ConfigDict({}) Extra parameters
                                 for the selection strategy.
              'num_elements': Integer. The number of elements to select in this 
                              cycle.
              'selection_columns': List of strings. Columns from the csv to keep 
                                   in the selection file.
          }),
      'metadata': String. Description of this active learning experiment or cycle.
      'cycle_dir': String. Location to write predictions and selections to.
      'training_pool': String. Examples to train the model for this cycle on.
      'virtual_library': String. Selection pool for this cycle.
  })
```

Sample command with test data:

```
python3 -m single_cycle_main \
  --cycle_config ./configs/simple_greedy_gaussian_process.py \
  --cycle_config.cycle_dir ./cycle_1 \
  --cycle_config.training_pool ./data/test/initial_training_test.csv \
  --cycle_config.virtual_library=./data/test/virtual_library.csv
```

Sample command with prod data:

```
python3 -m single_cycle_main \
  --cycle_config ./configs/simple_greedy_gaussian_process.py \
  --cycle_config.cycle_dir ./cycle_prod_1 \
  --cycle_config.training_pool ./tyk2_dataset/initial_tyk2_training.csv \
  --cycle_config.virtual_library=./tyk2_dataset/tyk2_fep_1.csv
```