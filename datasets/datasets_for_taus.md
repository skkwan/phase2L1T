# Datasets used for taus

## Gluon-gluon fusion Higgs to tau tau
Use `aodsim-two-file-batch` and the instructions in that `README.md` (the scripts read each AODSIM and then look for corresponding
mini and raw files)

### 200 pileup
MINIAODSIM:
`/GluGluHToTauTau_M125_14TeV_powheg_pythia8/PhaseIIMTDTDRAutumn18MiniAOD-PU200_103X_upgrade2023_realistic_v2-v1/MINIAODSIM`

FEVT:
`/GluGluHToTauTau_M125_14TeV_powheg_pythia8/PhaseIIMTDTDRAutumn18DR-PU200_103X_upgrade2023_realistic_v2-v1/FEVT`

### No pileup
`/GluGluHToTauTau_M125_14TeV_powheg_pythia8_TuneCP5/PhaseIITDRSpring19MiniAOD-NoPU_106X_upgrade2023_realistic_v3-v2/MINIAODSIM`



## Drell-Yan to ll
Use `two-file-batch-example` and the instructions in that `README.md` (the scripts read each MINIAODSIM and then look
for corresponding FEVT files)

### 200 pileup
MINIAODSIM:
`/DYToLL_M-50_14TeV_pythia8_pilot1/PhaseIIMTDTDRAutumn18MiniAOD-PU200_pilot_103X_upgrade2023_realistic_v2-v1/MINIAODSIM`

FEVT:
`/DYToLL_M-50_14TeV_TuneCP5_pythia8/PhaseIIMTDTDRAutumn18DR-PU200_103X_upgrade2023_realistic_v2-v2/FEVT`

### No pileup
`/DYToLL_M-50_14TeV_TuneCP5_pythia8/PhaseIIMTDTDRAutumn18MiniAOD-NoPU_103X_upgrade2023_realistic_v2-v2/MINIAODSIM`
which contains the MINIAODSIM files (listed in reverse alphabetical order on DAS website)

## Neutrino Gun
### 200 pileup
FEVT:
`/NeutrinoGun_E_10GeV/PhaseIIMTDTDRAutumn18DR-PU200_103X_upgrade2023_realistic_v2-v1/FEVT`



     