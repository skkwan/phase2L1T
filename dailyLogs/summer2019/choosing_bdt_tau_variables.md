# Week of June 27 - July 4, 2019

## BDT variable rankings (Friday June 28, 2019)

### Revisiting previously trained BDT on tau DYLL file
- In `outfile_tau_dyll_BDT`, we can see that I trained on l1Pt, l1Eta, l1Phi, l1Time, l1Iso, l1Iso_time, track12DZ, track13DZ, track1PVDZ, track2PVDZ, track3PVDZ, track1nStubs, track2nStubs, track1Time, track2Time, track3Time, l1DecayMode, track1ChiSquared, track2ChiSquared, track3ChiSquared, zVTX, track1Z, track2Z, track3Z, tauL1StripPt, tauL1StripEta, tauL1StripDR, pfCand1HoE,  pfCand2Hoe, pfCand3HoE, tauL1nEG, tauL1EGPt, l1TauEGTime.
- The variable rankings are:

| Rank | Variable         | Variable Importance |
|------|------------------|---------------------|
| 1    | l1Pt             | 6.555e-02           |
| 2    | zVTX             | 5.964e-02           |
| 3    | l1Eta            | 5.763e-02           |
| 4    | track1ChiSquared | 5.604e-02           |
| 5    | l1Time           | 5.589e-02           |
| 6    | l1Phi            | 5.520e-02           |
| 7    | taul1nEG         | 5.376e-02           |
| 8    | track1PVDZ       | 5.328e-02           |
| 9    | track1Z          | 5.303e-02           |
| 10   | tauL1StripDR     | 5.182e-02           |

The plots of these variables are in commit 6ac93f2 - should add these to weekly meeting slides

```https://github.com/skkwan/phase2L1BTagAnalyzer/tree/master/tau_exercise/plot_tau_features/validationPlots/dyll_root```

- In `TMVAAnalysis_tau.C`, I am currently defining signal to be genPt > 20 and all background to be genPt < 20. This is ok because gen variables are from MC, and we're interested in the l1 variables.
- signalCut and backgroundCut are both pfCand2HoE < 20 && pfCand3HoE < 20 && l1TauEGTime < 30.
- The new variable rankings are:

| Rank | Variable         | Variable Importance |
|------|------------------|---------------------|
| 1    | l1Eta            | 2.249e-01           |
| 2    | l1Pt             | 1.979e-01           |
| 3    | track1ChiSquared | 1.960e-01           |
| 4    | zVTX             | 1.913e-01           |
| 5    | l1Time           | 1.899e-01           |

- And the other information is: (open this in a text editor to properly read it)
<details><summary>Variable mean, RMS, min, max; ROC integration, testing efficiency</summary>
<p>

<HEADER> TFHandler_BDT            :         Variable                Mean                RMS        [        Min                Max ]
                         : ---------------------------------------------------------------------------------------------------
                         :             l1Pt:            17.946            27.771   [            5.2514            6349.5 ]
                         :             zVTX:          0.051779            3.7025   [           -14.152            14.988 ]
                         :            l1Eta:         0.0053034            1.9605   [           -2.5955            2.5914 ]
                         : track1ChiSquared:            29.799            23.754   [          0.090978            145.84 ]
                         :           l1Time:            7.7728            5.8369   [            0.0000            18.995 ]
                         : ---------------------------------------------------------------------------------------------------
                         :
                         : Evaluation results ranked by best signal efficiency and purity (area)
                         : -------------------------------------------------------------------------------------------------------------------
                         : DataSet       MVA
                         : Name:         Method:          ROC-integ
                         : dataset       BDT            : 0.949
                         : -------------------------------------------------------------------------------------------------------------------
                         :
                         : Testing efficiency compared to training efficiency (overtraining check)
                         : -------------------------------------------------------------------------------------------------------------------
                         : DataSet              MVA              Signal efficiency: from test sample (from training sample)
                         : Name:                Method:          @B=0.01             @B=0.10            @B=0.30
                         : -------------------------------------------------------------------------------------------------------------------
                         : dataset              BDT            : 0.174 (0.215)       0.905 (0.903)      0.979 (0.978)
                         : -------------------------------------------------------------------------------------------------------------------
                         :
<HEADER> Dataset:dataset          : Created tree 'TestTree' with 56169 events
                         :
<HEADER> Dataset:dataset          : Created tree 'TrainTree' with 56169 events

</p>
</details>

### Brainstorming variable binning
- As a side note, typically an offline cut with require that pT > 100 GeV should be accepted and anything below pT < 20 GeV should be rejected (because jet fake rates are super high at low pT), but we should not exclude l1Pt < 20 GeV from training/ algorithm development.

- For example:

| Variable | Range         | Discriminant value | Value used to calculate the discriminant |
|----------|---------------|--------------------|------------------------------------------|
| l1Pt     | 0 - 20 GeV    | ?                  | 10 GeV                                   |
| l1Pt     | 20 - 50 GeV   | ?                  | 35 GeV                                   |
| l1Pt     | 50 - 100 GeV  | ?                  | 75 GeV                                   |
| l1Pt     | 100 - 500 GeV | ?                  | 300 GeV                                  |

Looking at [this plot](https://github.com/skkwan/phase2L1BTagAnalyzer/blob/devel/tau_exercise/plot_tau_features/validationPlots/dyll_root/dyll_l1Eta.png) (commit d7c97ec), I arbitrarily decided this initial binning:

| Variable | Range        | Discriminant value | Value used to calculate the discriminant |
|----------|--------------|--------------------|------------------------------------------|
| l1Eta    | -3.0 to -1.5 | ?                  | -2.25                                    |
| l1Eta    | -1.5 to 0.0  | ?                  | -0.75                                    |
| l1Eta    | 0.0 to +1.5  | ?                  | +0.75                                    |
| l1Eta    | +1.5 to +3.0 | ?                  | +2.25                                    |




- Once I determine variable binning, I need to make an n-tuple with custom entries and values. 
   * ~~Look for a TMVA method that applies the trained BDT's weights~~ (It already does that)
   * Make TTree with (n1 * n2 * n3 etc.) entries.

- Ideally the options supported are:
   - A flag that changes what value to put in the n-tuple (midpoint vs. upper point vs. endpoint?)
   - Will add more as I think of them.

 
## Choosing BDT variables (Saturday June 29, 2019)

- Isobel suggests using *l1Pt, l1Eta, track1ChiSquared, tauL1StripPt, and l1DecayMode*.
- Re-trained BDT (commit `03e5cfe`)
<details><summary>Variable mean, RMS, min, max; ROC integration, testing efficiency</summary>
<p>

<HEADER> TFHandler_BDT            :         Variable                Mean                RMS        [        Min                Max ]
                         : ---------------------------------------------------------------------------------------------------
                         :             l1Pt:            17.847            7.7375   [            5.2514            668.00 ]
                         :            l1Eta:         0.0092128            1.9602   [           -2.5923            2.5906 ]
                         :     tauL1StripPt:            13.731            30.479   [            0.0000            6347.0 ]
                         : track1ChiSquared:            29.784            23.708   [          0.031723            145.84 ]
                         :      l1DecayMode:            1.2416            1.4663   [            0.0000            10.000 ]
                         : ---------------------------------------------------------------------------------------------------
                         :
                         : Evaluation results ranked by best signal efficiency and purity (area)
                         : ------------------------------------------------------------------------------------------------------------\
-------
                         : DataSet       MVA
                         : Name:         Method:          ROC-integ
                         : dataset       BDT            : 0.939
                         : ------------------------------------------------------------------------------------------------------------\
-------
                         :
                         : Testing efficiency compared to training efficiency (overtraining check)
                         : ------------------------------------------------------------------------------------------------------------\
-------
                         : DataSet              MVA              Signal efficiency: from test sample (from training sample)
                         : Name:                Method:          @B=0.01             @B=0.10            @B=0.30
                         : ------------------------------------------------------------------------------------------------------------\
-------
                         : dataset              BDT            : 0.233 (0.255)       0.814 (0.817)      0.976 (0.976)
                         : ------------------------------------------------------------------------------------------------------------\
-------
                         :
<HEADER> Dataset:dataset          : Created tree 'TestTree' with 56169 events
                         :
<HEADER> Dataset:dataset          : Created tree 'TrainTree' with 56169 events
</p>
</details>

-  Opened resulting ROOT file and looked at its contents:
<details><summary>Structure of post-TMVA file</summary>
<p>
root [9] _file0->cd()
(bool) true
root [10] .ls
TFile**		TMVA_training_taus_out_top5varsdyll.root	
 TFile*								TMVA_training_taus_out_top5varsdyll.root	
  TDirectoryFile*												dataset	dataset
   TDirectoryFile*													Method_BDT Directory for all BDT methods
    TDirectoryFile*														   BDT	     BDT
     KEY: TObjString														   TrainingPath;1 Collectable string class
     KEY: TObjString														   WeightFileName;1	      Collectable string class
     KEY: TTree															   MonitorNtuple;1	      BDT variables
     KEY: TH1D															   MVA_BDT_S;1		      MVA_BDT_S
     KEY: TH1D															   MVA_BDT_B;1		      MVA_BDT_B
     KEY: TH1D															   MVA_BDT_S_high;1	      MVA_BDT_S_high
     KEY: TH1D															   MVA_BDT_B_high;1	      MVA_BDT_B_high
     KEY: TH1D															   MVA_BDT_effS;1	      MVA_BDT (signal)
     KEY: TH1D															   MVA_BDT_effB;1	      MVA_BDT (background)
     KEY: TH1D															   MVA_BDT_effBvsS;1	      MVA_BDT
     KEY: TH1D															   MVA_BDT_rejBvsS;1	      MVA_BDT
     KEY: TH1D															   MVA_BDT_invBeffvsSeff;1    MVA_BDT
     KEY: TH1D															   MVA_BDT_Train_S;1	      MVA_BDT_Train_S
     KEY: TH1D															   MVA_BDT_Train_B;1	      MVA_BDT_Train_B
     KEY: TH1D															   MVA_BDT_trainingEffS;1     MVA_BDT (signal)
     KEY: TH1D															   MVA_BDT_trainingEffB;1     MVA_BDT (background)
     KEY: TH1D															   MVA_BDT_trainingEffBvsS;1  MVA_BDT
     KEY: TH1D															   MVA_BDT_trainingRejBvsS;1  MVA_BDT
     KEY: TH1F															   l1Pt__Signal;1	      l1Pt
     KEY: TH1F															   l1Pt__Background;1	      l1Pt
     KEY: TH1F															   l1Eta__Signal;1	      l1Eta
     KEY: TH1F															   l1Eta__Background;1	      l1Eta
     KEY: TH1F															   tauL1StripPt__Signal;1     tauL1StripPt
     KEY: TH1F															   tauL1StripPt__Background;1 tauL1StripPt
     KEY: TH1F															   track1ChiSquared__Signal;1 track1ChiSquared
     KEY: TH1F															   track1ChiSquared__Background;1 track1ChiSquared
     KEY: TH1F															   l1DecayMode__Signal;1	  l1DecayMode
     KEY: TH1F															   l1DecayMode__Background;1	  l1DecayMode
     KEY: TDirectoryFile													   CorrelationPlots;1		  CorrelationPlots
     KEY: TH1F															   dataset_BoostWeight;1	  AdaBooost weight distribution
     KEY: TH1F															   BoostWeightVsTree;1		  Boost weights vs tree
     KEY: TH1F															   ErrFractHist;1		  error fraction vs tree number
     KEY: TH1I															   dataset_NodesBeforePruning;1	  nodes before pruning
     KEY: TH1I															   dataset_NodesAfterPruning;1	  nodes after pruning
    KEY: TDirectoryFile														   BDT;1			  BDT
   KEY: TH2F															   CorrelationMatrixS;1		  Correlation Matrix (signal)
   KEY: TH2F															   CorrelationMatrixB;1		  Correlation Matrix (background)
   KEY: TDirectoryFile														   InputVariables_Id;1		  InputVariables_Id
   KEY: TDirectoryFile														   Method_BDT;1			  Directory for all BDT methods
   KEY: TTree															   TestTree;1			  TestTree
   KEY: TTree															   TrainTree;1			  TrainTree
  KEY: TDirectoryFile														   dataset;1			  dataset

</p>
</details>

