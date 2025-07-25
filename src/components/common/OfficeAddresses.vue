<template>
  <div id="office-addresses">
    <!-- EDIT SECTION -->
    <template v-if="isEditing">
      <!-- Registered Office -->
      <v-row
        no-gutters
        class="address-edit-header"
      >
        <v-col
          cols="12"
          sm="3"
        >
          <label class="section-title">Registered Office</label>
        </v-col>
        <v-col
          cols="12"
          sm="9"
        />
      </v-row>

      <v-card
        flat
        class="py-8 px-6"
      >
        <!-- Registered Mailing Address -->
        <v-row
          no-gutters
          class="edit-section"
        >
          <v-col
            cols="12"
            sm="3"
            class="pr-4"
          >
            <label class="title-label">Mailing Address</label>
          </v-col>

          <v-col
            cols="12"
            sm="9"
            class="pt-4 pt-sm-0"
          >
            <MailingAddress
              id="address-registered-mailing"
              ref="regMailingAddress"
              :address="mailingAddress"
              :editing="true"
              :schema="OfficeAddressSchema"
              @update:address="updateAddress('mailingAddress', mailingAddress, $event)"
              @valid="updateAddressValid('mailingAddress', $event)"
            />
          </v-col>
        </v-row>

        <!-- Registered Delivery Address -->
        <v-row
          no-gutters
          class="edit-section"
        >
          <v-col
            cols="12"
            sm="3"
            class="pr-4"
          >
            <label class="title-label">Delivery Address</label>
          </v-col>

          <v-col
            cols="12"
            sm="9"
            class="pt-4 pt-sm-0"
          >
            <v-checkbox
              id="registered-mailing-same-chkbx"
              v-model="inheritMailingAddress"
              class="inherit-checkbox"
              hide-details
              label="Same as Mailing Address"
              @change="setDeliveryAddressToMailingAddress()"
            />
            <template
              v-if="!isSame(mailingAddress, deliveryAddress, ['actions']) || !inheritMailingAddress"
            >
              <DeliveryAddress
                v-if="!inheritMailingAddress"
                id="address-registered-delivery"
                ref="regDeliveryAddress"
                class="pt-6"
                :address="deliveryAddress"
                :editing="true"
                :schema="OfficeAddressSchema"
                :noPoBox="true"
                @update:address="updateAddress('deliveryAddress', deliveryAddress, $event)"
                @valid="updateAddressValid('deliveryAddress', $event)"
              />
            </template>
          </v-col>
        </v-row>
      </v-card>

      <!--Records Office Address -->
      <template v-if="!isEntityCoop">
        <!-- Records Office -->
        <v-row
          no-gutters
          class="address-edit-header"
        >
          <v-col
            cols="12"
            sm="3"
          >
            <label class="section-title">Records Office</label>
          </v-col>

          <v-col
            cols="12"
            sm="9"
          >
            <v-checkbox
              id="records-mailing-same-chkbx"
              v-model="inheritRegisteredAddress"
              class="inherit-checkbox"
              hide-details
              label="Same as Registered Office"
              @change="setRecordOfficeToRegisteredOffice()"
            />
          </v-col>
        </v-row>

        <template v-if="!inheritRegisteredAddress">
          <v-card
            flat
            class="py-8 px-6"
          >
            <!-- Records Mailing Address -->
            <v-row
              no-gutters
              class="edit-section"
            >
              <v-col
                cols="12"
                sm="3"
                class="pr-4"
              >
                <label class="title-label">Mailing Address</label>
              </v-col>

              <v-col
                cols="12"
                sm="9"
                class="pt-4 pt-sm-0"
              >
                <MailingAddress
                  id="address-records-mailing"
                  ref="recMailingAddress"
                  :address="recMailingAddress"
                  :editing="true"
                  :schema="OfficeAddressSchema"
                  @update:address="updateAddress('recMailingAddress', recMailingAddress, $event)"
                  @valid="updateAddressValid('recMailingAddress', $event)"
                />
              </v-col>
            </v-row>

            <!-- Registered Delivery Address -->
            <v-row
              no-gutters
              class="edit-section"
            >
              <v-col
                cols="12"
                sm="3"
                class="pr-4"
              >
                <label class="title-label">Delivery Address</label>
              </v-col>

              <v-col
                cols="12"
                sm="9"
                class="pt-4 pt-sm-0"
              >
                <v-checkbox
                  v-model="inheritRecMailingAddress"
                  class="inherit-checkbox"
                  hide-details
                  label="Same as Mailing Address"
                  @change="setRecordDeliveryAddressToMailingAddress()"
                />
                <template
                  v-if="!isSame(recMailingAddress, recDeliveryAddress, ['actions']) || !inheritRecMailingAddress"
                >
                  <DeliveryAddress
                    id="address-records-delivery"
                    ref="recDeliveryAddress"
                    class="pt-6"
                    :address="recDeliveryAddress"
                    :editing="true"
                    :schema="OfficeAddressSchema"
                    :noPoBox="true"
                    @update:address="updateAddress('recDeliveryAddress', recDeliveryAddress, $event)"
                    @valid="updateAddressValid('recDeliveryAddress', $event)"
                  />
                </template>
              </v-col>
            </v-row>
          </v-card>
        </template>
      </template>
    </template>

    <!-- SUMMARY SECTION -->
    <template v-else>
      <v-row
        id="summary-registered-address"
        no-gutters
      >
        <v-col
          cols="12"
          sm="3"
          class="pr-4"
        >
          <label>Registered Office</label>
        </v-col>

        <v-col
          cols="12"
          sm="4"
          class="pr-8 pt-4 pt-sm-0"
        >
          <label class="summary-section-header">Mailing Address</label>
          <div v-if="isEmptyAddress(mailingAddress)">
            (Not entered)
          </div>
          <MailingAddress
            v-else
            :address="mailingAddress"
            :editing="false"
          />
        </v-col>

        <v-col
          cols="12"
          sm="4"
          class="pr-8 pt-4 pt-sm-0"
        >
          <label class="summary-section-header">Delivery Address</label>
          <div v-if="isEmptyAddress(deliveryAddress)">
            (Not entered)
          </div>
          <div v-else-if="isSame(mailingAddress, deliveryAddress)">
            Same as Mailing Address
          </div>
          <DeliveryAddress
            v-else
            :address="deliveryAddress"
            :editing="false"
          />
        </v-col>
      </v-row>

      <v-row
        v-if="!isEntityCoop"
        id="summary-records-address"
        no-gutters
        class="mt-6"
      >
        <v-col
          cols="12"
          sm="3"
          class="pr-4"
        >
          <label>Records Office</label>
        </v-col>

        <v-col
          cols="12"
          sm="4"
          class="pr-8 pt-4 pt-sm-0"
        >
          <label class="summary-section-header">Mailing Address</label>
          <div v-if="isEmptyAddress(recMailingAddress)">
            (Not entered)
          </div>
          <div v-else-if="isSame(mailingAddress, recMailingAddress)">
            Same as Registered Office
          </div>
          <MailingAddress
            v-else
            :address="recMailingAddress"
            :editing="false"
          />
        </v-col>

        <v-col
          cols="12"
          sm="4"
          class="pr-8 pt-4 pt-sm-0"
        >
          <label class="summary-section-header">Delivery Address</label>
          <div v-if="isEmptyAddress(recDeliveryAddress)">
            (Not entered)
          </div>
          <div v-else-if="isSame(deliveryAddress, recDeliveryAddress)">
            Same as Registered Office
          </div>
          <div v-else-if="isSame(recMailingAddress, recDeliveryAddress)">
            Same as Mailing Address
          </div>
          <DeliveryAddress
            v-else
            :address="recDeliveryAddress"
            :editing="false"
          />
        </v-col>
      </v-row>
    </template>
  </div>
</template>

<script lang="ts">
import { Component, Emit, Mixins, Prop, Watch } from 'vue-property-decorator'
import { Getter } from 'pinia-class'
import { useStore } from '@/store/store'
import { OfficeAddressSchema } from '@/schemas'
import { BaseAddress } from '@bcrs-shared-components/base-address'
import { AddressIF, DefineCompanyIF, EmptyAddress, RegisteredRecordsAddressesIF } from '@/interfaces'
import { CommonMixin } from '@/mixins'

@Component({
  components: {
    DeliveryAddress: BaseAddress,
    MailingAddress: BaseAddress
  }
})
export default class OfficeAddresses extends Mixins(CommonMixin) {
  // Refs for sbc common base address components so we can access form validation
  $refs!: {
    regMailingAddress: any
    regDeliveryAddress: any
    recMailingAddress: any
    recDeliveryAddress: any
  }

  /**
   * Addresses object from the parent page.
   * If this is null then this is a new filing; otherwise these are the addresses from a draft/navigation
   * from other step.
   * This will be emitted back to the parent page when the addresses are updated.
   */
  @Prop({ default: null }) readonly inputAddresses!: RegisteredRecordsAddressesIF

  /** Whether to show the editable forms for the addresses (true) or just the static display addresses (false). */
  @Prop({ default: true }) readonly isEditing!: boolean

  @Prop({ default: false }) readonly showErrors!: boolean

  @Getter(useStore) getDefineCompanyStep!: DefineCompanyIF
  @Getter(useStore) isBaseCompany!: boolean
  @Getter(useStore) isEntityCoop!: boolean

  // Local properties
  protected addresses: RegisteredRecordsAddressesIF = this.inputAddresses
  readonly defaultAddress: AddressIF = {
    ...EmptyAddress,
    addressRegion: 'BC'
  }

  // The 4 addresses that are the current state of the BaseAddress components:
  mailingAddress = {} as AddressIF
  deliveryAddress = {} as AddressIF
  recMailingAddress = {} as AddressIF
  recDeliveryAddress = {} as AddressIF

  // Validation events from BaseAddress:
  private mailingAddressValid = true
  private deliveryAddressValid = true
  private recMailingAddressValid = true
  private recDeliveryAddressValid = true

  /**
   * State of the checkbox for determining whether the Delivery address is the same as the Mailing address.
   */
  inheritMailingAddress = true

  /**
   * State of the checkbox for determining whether or not the mailing address is the same as the delivery address.
   * For Records Office.
   */
  inheritRecMailingAddress = true

  /**
   * State of the checkbox for determining whether the Record address is the same as the Registered address.
   */
  inheritRegisteredAddress = true

  // Imports for template
  readonly OfficeAddressSchema = OfficeAddressSchema

  /** Called when component is created. */
  created (): void {
    // on first load, determine inherited flags based on address values and update parent
    this.setAddresses(true)
    this.emitValid()
  }

  /**
   * Sets address data.
   * @param loadInheritedFlags used to update inherited flags based on isSame checks if true
   */
  private setAddresses (loadInheritedFlags: boolean): void {
    if (this.addresses.registeredOffice) {
      this.mailingAddress = this.addresses.registeredOffice.mailingAddress
      this.deliveryAddress = this.addresses.registeredOffice.deliveryAddress

      const isNew = this.isEmptyAddress(this.addresses.registeredOffice.mailingAddress) &&
              this.isEmptyAddress(this.addresses.registeredOffice.deliveryAddress)

      if (loadInheritedFlags) {
        if (this.showDeliveryAddressByDefault) {
          this.inheritMailingAddress = !(isNew) &&
            this.isSame(
              this.addresses.registeredOffice.mailingAddress,
              this.addresses.registeredOffice.deliveryAddress
            )
          // if it is a newly created IA, the delivery address should not be sychronized with mailing address
          if (!this.inheritMailingAddress && isNew) {
            this.deliveryAddress = this.defaultAddress
          }
        } else {
          this.inheritMailingAddress = this.isSame(
            this.addresses.registeredOffice.mailingAddress,
            this.addresses.registeredOffice.deliveryAddress
          )
        }
      }
      if (this.isBaseCompany) {
        this.recMailingAddress = this.addresses.recordsOffice?.mailingAddress
        this.recDeliveryAddress = this.addresses.recordsOffice?.deliveryAddress

        if (loadInheritedFlags) {
          this.inheritRegisteredAddress = (
            this.isSame(
              this.addresses.registeredOffice.deliveryAddress,
              this.addresses.recordsOffice?.deliveryAddress
            ) &&
            this.isSame(
              this.addresses.registeredOffice.mailingAddress,
              this.addresses.recordsOffice?.mailingAddress
            )
          )

          this.inheritRecMailingAddress = this.isSame(
            this.addresses.recordsOffice?.mailingAddress,
            this.addresses.recordsOffice?.deliveryAddress
          )
        }
      }
    }
  }

  // Getters (Computed Values)
  /** Whether the address form is valid. */
  get formValid (): boolean {
    const registeredOfficeValid = this.mailingAddressValid &&
      (this.deliveryAddressValid || this.inheritMailingAddress)

    const recordsOfficeValid = this.inheritRegisteredAddress ||
      (this.recMailingAddressValid && (this.inheritRecMailingAddress || this.recDeliveryAddressValid))

    return registeredOfficeValid && recordsOfficeValid
  }

  /** Whether to show the delivery address by default. */
  get showDeliveryAddressByDefault (): boolean {
    // only coops and corps
    return (this.isEntityCoop || this.isBaseCompany)
  }

  //
  // Event Handlers
  //

  /** Sets the Registered Delivery Address to the Registered Mailing Address. */
  setDeliveryAddressToMailingAddress (): void {
    if (this.inheritMailingAddress) {
      this.deliveryAddress = { ...this.mailingAddress }
    } else {
      // Clear to default
      this.deliveryAddress = { ...this.defaultAddress }
    }

    // Records delivery address also needs to be updated if inherited
    if (this.inheritRegisteredAddress) {
      this.recDeliveryAddress = { ...this.deliveryAddress }
    }

    this.emitAddresses()
  }

  /** Sets the Records office addresses to the Registered office addresses. */
  setRecordOfficeToRegisteredOffice (): void {
    if (this.inheritRegisteredAddress) {
      this.recDeliveryAddress = { ...this.deliveryAddress }
      this.recMailingAddress = { ...this.mailingAddress }
    } else {
      this.inheritRecMailingAddress = this.inheritMailingAddress
      // Clear to defaults
      this.recMailingAddress = { ...this.defaultAddress }
      this.recDeliveryAddress = { ...this.defaultAddress }
    }
    this.emitAddresses()
  }

  /** Sets the Records Delivery Address to Records Mailing Address. */
  setRecordDeliveryAddressToMailingAddress (): void {
    if (this.inheritRecMailingAddress) {
      this.recDeliveryAddress = { ...this.recMailingAddress }
    } else {
      // Clear to default
      this.recDeliveryAddress = { ...this.defaultAddress }
    }
    this.emitAddresses()
  }

  /**
   * Updates the specified address' data.
   * @param addressToUpdate the address type to set the data of
   * @param baseAddress the address object to update
   * @param newAddress the new address data
   */
  updateAddress (addressToUpdate: string, baseAddress: AddressIF, newAddress: AddressIF): void {
    Object.assign(baseAddress, newAddress)
    switch (addressToUpdate) {
      case 'mailingAddress':
        if (this.inheritMailingAddress) {
          this.deliveryAddress = { ...newAddress }
        }
        if (this.inheritRegisteredAddress) {
          this.recMailingAddress = { ...newAddress }
          this.recDeliveryAddress = { ...this.deliveryAddress }
        }
        break
      case 'deliveryAddress':
        if (this.inheritRegisteredAddress) {
          this.recDeliveryAddress = { ...newAddress }
        }
        break
      case 'recMailingAddress':
        if (this.inheritRecMailingAddress) {
          this.recDeliveryAddress = { ...newAddress }
        }
        break
      case 'recDeliveryAddress':
        // nothing to do
        break
      default:
        // eslint-disable-next-line no-console
        console.log(`Error: cannot update ${addressToUpdate}`)
    }
    this.emitAddresses()
  }

  /**
   * Updates the specified address' validity.
   * @param addressToValidate the address type to set the validity of
   * @param valid whether the address is valid
   */
  updateAddressValid (addressToValidate: string, valid: boolean): void {
    switch (addressToValidate) {
      case 'deliveryAddress':
        this.deliveryAddressValid = valid
        break
      case 'mailingAddress':
        this.mailingAddressValid = valid
        break
      case 'recDeliveryAddress':
        this.recDeliveryAddressValid = valid
        break
      case 'recMailingAddress':
        this.recMailingAddressValid = valid
        break
      default:
        // eslint-disable-next-line no-console
        console.log(`Error: cannot validate ${addressToValidate}`)
    }
    this.emitValid()
  }

  // Watchers
  @Watch('formValid')
  private onFormValidityChange (): void {
    this.emitValid()
  }

  @Watch('getDefineCompanyStep.officeAddresses', { deep: true, immediate: true })
  private updateAddresses (): void {
    this.addresses = this.inputAddresses
    this.setAddresses(false)
    this.emitValid()
  }

  // Watchers
  @Watch('showErrors')
  private onShowErrorsChanged (): void {
    // Check if addresses are valid
    if (this.showErrors && this.isEditing) {
      // Registered Mailing Address
      this.$refs.regMailingAddress.$refs.addressForm.validate()
      if (!this.inheritMailingAddress) {
        // Registered Delivery Address
        this.$refs.regDeliveryAddress.$refs.addressForm.validate()
      }

      if (!this.isEntityCoop && !this.inheritRegisteredAddress) {
        // Records Mailing Address
        this.$refs.recMailingAddress.$refs.addressForm.validate()
        if (!this.inheritRecMailingAddress) {
          // Records Delivery Address
          this.$refs.recDeliveryAddress.$refs.addressForm.validate()
        }
      }
    }
  }

  // Event Emitters
  /** Emits the valid state of this address form. */
  @Emit('valid')
  private emitValid (): boolean {
    return this.formValid
  }

  /** Emits updated addresses object to the parent page. */
  @Emit('update:addresses')
  private emitAddresses (): any {
    if (!this.isEntityCoop) {
      return {
        registeredOffice: {
          deliveryAddress: this.deliveryAddress,
          mailingAddress: this.mailingAddress
        },
        recordsOffice: {
          deliveryAddress: this.recDeliveryAddress,
          mailingAddress: this.recMailingAddress
        }
      }
    } else {
      return {
        registeredOffice: {
          deliveryAddress: this.deliveryAddress,
          mailingAddress: this.mailingAddress
        }
      }
    }
  }
}
</script>

<style lang="scss" scoped>
@import '@/assets/styles/theme.scss';

.address-edit-header {
  background-color: rgba(1, 51, 102, 0.15);
  padding: 1.25rem;
}

.edit-section {
  font-size: $px-16;
  color: $gray7;
}

.section-title,
.title-label,
.item-label,
.summary-section-title {
  font-weight: bold;
  color: $gray9;
}

.inherit-checkbox {
  margin-top: 0;
  padding-top: 0;
  margin-left: -3px;
}

.summary-section-header {
  font-size: $px-14;
  font-weight: bold;
}

#summary-registered-address label,
#summary-records-address label {
  font-weight: bold;
}
</style>
