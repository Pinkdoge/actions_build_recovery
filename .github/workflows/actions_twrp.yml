name: twrp-building
on:
  watch:
    types: [started]
jobs:
  build:
    if: github.event.repository.owner.id == github.event.sender.id
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@master
    - name: Clean Up
      run: |
        docker rmi `docker images -q`
        sudo rm -rf /usr/share/dotnet /etc/mysql /etc/php /etc/sudo apt/sources.list.d
        sudo apt -y purge azure-cli ghc* zulu* hhvm llvm* firefox google* dotnet* powershell openjdk* mysql* php*
        sudo apt update
        sudo apt -y autoremove --purge
        sudo apt clean 
    - name: Update packages
      run: |
        sudo apt update
        sudo apt full-upgrade
    - name: Install required packages
      run: sudo apt install git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev tree lib32z-dev libgl1-mesa-dev libxml2-utils xsltproc unzip jq
    - name: Get variables
      run: |
        echo "::set-output name=twrp_url::$(jq -r '.twrp_url' config.json)"
        echo "::set-output name=twrp_branch::$(jq -r '.twrp_branch' config.json)"
        echo "::set-output name=git_username::$(jq -r '.git_username' config.json)"
        echo "::set-output name=git_email::$(jq -r '.git_email' config.json)"
        echo "::set-output name=use_own_dt::$(jq -r '.use_own_dt' config.json)"
        echo "::set-output name=dt_url::$(jq -r '.dt_url' config.json)"
        echo "::set-output name=dt_remote::$(jq -r '.dt_remote' config.json)"
        echo "::set-output name=dt_branch::$(jq -r '.dt_branch' config.json)"
        echo "::set-output name=dt_path::$(jq -r '.dt_path' config.json)"
        echo "::set-output name=device_code::$(jq -r '.device_code' config.json)"
        echo "::set-output name=fix_product::$(jq -r '.fix_product' config.json)"
        echo "::set-output name=fix_misscom::$(jq -r '.fix_misscom' config.json)"
        echo "::set-output name=fix_busybox::$(jq -r '.fix_busybox' config.json)"
        echo "::set-output name=fix_branch::$(jq -r '.fix_branch' config.json)"
        echo "::set-output name=date::$(date +%F)"
      id: var
    - name: Install Repo
      run: |
        mkdir ~/bin
        curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
        chmod a+x ~/bin/repo
    - name: Initialize Repair Manifest
      run: git clone https://github.com/TeamWin/buildtree_manifests.git repair/
    - name: Initialize a Repo client
      run: |
        PATH=~/bin:$PATH
        mkdir workspace
        cd workspace
        echo "::set-output name=pwd::$(pwd)"
        git config --global user.name "${{ steps.var.outputs.git_username }}"
        git config --global user.email "${{ steps.var.outputs.git_email }}"
        repo init --depth=1 -u ${{ steps.var.outputs.twrp_url }} -b ${{ steps.var.outputs.twrp_branch }}
        mkdir .repo/local_manifests
      id: pwd
    - name: Fix the bug of missing common
      if: steps.var.outputs.fix_misscom == 'true'
      run: cp repair/omni-9.0/qcom.xml workspace/.repo/local_manifests/
    - name: Fix busybox bug
      if: steps.var.outputs.fix_busybox == 'true'
      run: cp repair/omni-9.0/busybox.xml workspace/.repo/local_manifests/
    - name: Clone your own device tree
      if: steps.var.outputs.use_own_dt == 'true'
      run: |
        sed -i 's!dt_url!${{ steps.var.outputs.dt_url }}!g' device.xml
        sed -i 's!dt_path!${{ steps.var.outputs.dt_path }}!g' device.xml
        sed -i 's/dt_remote/${{ steps.var.outputs.dt_remote }}/g' device.xml
        sed -i 's/dt_branch/${{ steps.var.outputs.dt_branch }}/g' device.xml
        cp device.xml workspace/.repo/local_manifests/   
    - name: Repo sync
      run: |
        PATH=~/bin:$PATH
        cd workspace
        repo sync -c -j$(nproc --all) --force-sync --no-clone-bundle --no-tags
        ls -al
    - name: Fix cannot locate product
      if: steps.var.outputs.fix_product == 'true'
      run: |
        cd ${{ steps.pwd.outputs.pwd }}/build/core
        rm -rf product_config.mk
        cp ${{ steps.pwd.outputs.pwd }}/../build/core/${{ steps.var.outputs.fix_branch }}/product_config.mk ${{ steps.pwd.outputs.pwd }}/build/core/product_config.mk
    - name: Start Building
      run: |
        PATH=~/bin:$PATH
        cd ${{ steps.pwd.outputs.pwd }}
        tree device
        export ALLOW_MISSING_DEPENDENCIES=true
        source build/envsetup.sh
        lunch omni_${{ steps.var.outputs.device_code }}-eng 
        mka recoveryimage -j$(nproc --all)
    - name: Upload TWRP
      uses: softprops/action-gh-release@v1
      with:
        files: workspace/out/target/product/${{ steps.var.outputs.device_code }}/recovery.img
        name: ${{ steps.var.outputs.date }} ${{ steps.var.outputs.device_code }} by ${{ steps.var.outputs.git_username }}
        tag_name: ${{ github.run_id }}
        body: Android Third-Party Recovery built by ${{ steps.var.outputs.git_username }}
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
